# Security

## What is encrypted
The whole vault payload: every account (website, email, password, URL, notes, recovery email and phone, PIN, security questions, password history, category, timestamps), the category list, the security and privacy settings and, when legacy access is on, the legacy key with its wraps.
Only the file header is plaintext: format version, KDF name and parameters, salt, cipher name, nonce. None of it is secret.

## Key derivation
- Master password → Unicode NFC → UTF-8 bytes.
- Argon2id (libsodium via NSec) with a 16-byte random salt, memory 256 MiB, 8 passes (about 0.85 s on the development PC), parallelism 1 → 32-byte key.
- The salt is generated when the vault is created and again on every master password change.
- Parameters live in the header so they can be raised later without breaking old vaults.
- If a vault opens with weaker parameters than the defaults (for example an old or test backup), Keyra re-encrypts it once with a new salt right after unlock, raising each parameter to at least the default. If that save fails, the file stays as it was.
- Before running Argon2id, the header parameters are bounds-checked so a modified file cannot make the app use huge memory or time.

## Encryption
- AES-256-GCM (`System.Security.Cryptography.AesGcm`, Windows CNG), 16-byte tag.
- A new random 12-byte nonce on every save. A key changes with every master password change and KDF upgrade; even 100 saves a day for 100 years (3.65 million) stays far below the 2^32 random-nonce limit.
- The whole header is passed as associated data. Changing any header byte (version, KDF parameters, salt, nonce) makes decryption fail.

## Where salts and nonces live
In the plaintext header of `vault.keyra`. See [vault-format.md](vault-format.md).

## What the master password protects
Everything in the vault. There is no recovery key and no stored hash.
A correct password is proven only by successful authenticated decryption.
The one other key is the optional legacy key (see [legacy-access.md](legacy-access.md)). It is stored only inside the encrypted vault. Any two of the three legacy shares unwrap it, and it opens only the legacy snapshot: a read-only copy of the accounts the owner chose. It never opens `vault.keyra`.

## If the master password is lost
The vault cannot be opened, by you or by anyone. There is no recovery. Backups use the same password that was active when they were made. If legacy access was set up, two shares still open the last legacy snapshot, read-only and with only the chosen accounts; it is not a backup.

## If the vault file is modified
GCM authentication fails. On the lock screen Keyra shows "Wrong password. Try again." and, from the second try, "Still not working? Check Caps Lock and the keyboard language. If you are sure the password is right, the file may be damaged: restore from a backup.", because a wrong password and a modified file look the same to the app (a property of authenticated encryption). Other dialogs that check a password (restore, change password) say "Unable to unlock vault. Check your master password." A damaged header or payload shows "The vault could not be authenticated or appears corrupted. Your original vault has not been modified." In every case Keyra shows no data, does not try other ways to decrypt, does not overwrite the file, and does not create a new vault. You can restore from a backup.
A file that is only locked or not downloaded yet (OneDrive) shows "The vault file could not be read right now", never the corrupted message.

## Saving
Every save writes a new temp file in the same folder, flushes it to disk, then renames it over `vault.keyra`. A crash during save leaves the old vault intact. In-memory data changes only after the save succeeded. Right before the rename Keyra checks that `vault.keyra` still has the bytes it read or last wrote (SHA-256); if another PC, OneDrive or another program changed or removed it, nothing is written and the vault locks, so a newer copy is never overwritten. A read-only vault file fails at once. Creating a new vault renames without overwrite, so it can never replace a vault that appeared meanwhile (for example a OneDrive download).

## Backup and restore
- Backup copies the encrypted file, only when it is the one Keyra knows (same check as saving). No plaintext is ever written.
- Files larger than 64 MB (vault, backup, legacy snapshot) or 16 MB (CSV) are not read, so a huge file cannot exhaust memory.
- Restore requires the backup's password, decrypts it first, keeps a flushed copy of the current vault, replaces it atomically only if it did not change meanwhile, then locks.
- There is no plaintext export. CSV import reads a plaintext file you made yourself; Keyra offers to delete it afterwards but cannot securely wipe it (SSD wear levelling, OneDrive versions, recycle bin).

## While unlocked
- The key stays in a pinned 32-byte array and is zeroed on lock.
- Account strings are normal .NET strings. The garbage collector may copy them and they cannot be reliably zeroed. Decrypted JSON byte buffers and password byte buffers are zeroed after use. `SecureString` is not used: Microsoft does not recommend it for new code, and WPF text boxes produce strings anyway.
- Secrets (password, notes, recovery phone, PIN, security answers, old passwords in the history) are masked with a fixed-length mask. Only one is shown at a time; it hides again after 20 seconds, on selection change, when a dialog opens or the details panel closes, when the window loses focus, or on lock. Secrets are never searched. In the editor they are typed into password boxes, read in code-behind, never bound.
- Lock happens on the Lock button, after inactivity (default 5 minutes), on minimize (with "Minimize to tray" on: lock, then hide), on Windows lock / user switch / remote disconnect, on suspend, and on exit. The tray icon exists only while Keyra is hidden and shows only "Keyra (locked)", "Open Keyra" and "Exit". Lock drops the accounts, zeroes the key, clears password boxes and dialogs, and clears a password Keyra put on the clipboard. A lock that happens while an unlock or a vault create is still running wins: the vault stays locked.

## Screen capture and shoulder surfing
- By default every Keyra window (main window, dialogs, tooltips, drop-downs, menus, file dialogs; not the tray icon menu, which shows no data and exists only while locked) is excluded from screenshots, recordings and screen sharing (Windows display affinity "exclude from capture"; black on older Windows). It is always on before unlock and on again after lock. You can allow capture in Settings → Advanced → Privacy after a confirmation.
- Limits: a camera pointed at the screen, other software running with your rights that reads Keyra's memory, or a remote-desktop session that copies the screen at a lower level are not stopped. A popup is blocked while Windows is about to show it (a hook on Keyra's UI thread), before its first frame; a second hook catches windows shown in other ways.
- "Blur the list" (off by default) blurs website and email of rows that are not hovered or selected, against people looking over your shoulder.

## Protected accounts (Windows Hello)
- An account can be marked "Protect with Windows Hello". Showing or copying its secrets (password, notes, recovery phone, PIN, security answers, old passwords), Edit, Delete, Restore, Clear history and turning protection off first ask Windows Hello, or the master password if Hello is not available or was canceled.
- This is a gate inside the unlocked app, not encryption. The vault is still encrypted only with the key from the master password; a protected account is stored the same way as any other. Hello keys, PINs or biometrics are never used to derive or store a key.
- A passed check opens only that account for 60 seconds; a dialog opened for it (editor, confirmation) keeps it open until the dialog closes. Lock ends every open account. Core itself refuses changes to a protected account without a passed check.
- The Health report never names an unprotected account that has the same password as a protected one.
- The master-password check runs Argon2id with the open vault's salt and parameters and compares the result with the key in memory in constant time. Nothing is stored. After 5 wrong master passwords in a row the vault locks. The current password in Change master password is the same check and counts too. Argon2 runs outside the vault's internal lock, so the window stays responsive.
- Limits: like everything in an unlocked session (T11, T12), the gate does not stop malware that reads Keyra's memory, where the decrypted accounts already are. It stops a person at the unlocked PC from simply clicking "show" or "copy".

## Clipboard
- Copied values are marked so Windows clipboard history (Win+V) and cloud clipboard do not keep them.
- After 30 seconds (configurable) Keyra clears the clipboard, but only if nothing else was copied since (checked with the Windows clipboard sequence number).
- Limits: other running apps can read the clipboard while the value is there. Some clipboard managers ignore the exclusion formats. If Keyra crashes on a background thread it locks and clears the copy first. If Keyra is killed, or on logoff or power loss, nothing can clear it.

## Logs and errors
The log stores only event names, exception type names and HResult codes. It never stores passwords, keys, account data, or exception messages. Error messages shown to the user never contain secrets. Automated tests check this.

## Network
Keyra makes no network calls. There is no telemetry, update check, or cloud API. Updates are installed by hand from GitHub Releases. The SHA-256 on the release page only shows the download is complete (it comes from the same page); the signer thumbprint published in README, SECURITY.md and on the release page (`AA4E8EA05917E2598282E8120D44F17A1178D97D`) catches a fake release with its own certificate, but all copies are under the same GitHub account. Release signatures are timestamped, so they stay valid after the certificate expires. Trusting the self-signed certificate means trusting its publisher. If the Documents folder is backed up by OneDrive, OneDrive syncs the encrypted file; Keyra does not talk to OneDrive. The "Open" button next to an account URL hands an http/https address to the default browser after a click; that is the browser's network traffic, not Keyra's, and the URL is never logged.

## Second start, tray and tour
- Starting Keyra again while it runs sets a session-local named event (`Local\Keyra.Vault.Show`). The running Keyra only shows its window (the lock screen when it was hidden in the tray, otherwise the window as it is); the event carries no data, and any program in the same session could at most bring that window forward. Only a running Keyra started from the same file is allowed to take the foreground.
- The guided tour shows fixed text only, never account data; it blocks keys to the screen behind it like a dialog and closes on lock.

## Legacy access
- Optional (details in [legacy-access.md](legacy-access.md)). Keyra makes a random legacy key and three random shares. For each pair of shares, HKDF-SHA256 (built into .NET) makes a pair key that wraps the legacy key with AES-256-GCM. Any two shares open the snapshot; one share alone opens nothing. No Shamir or other home-made math.
- The legacy key is stored only inside the encrypted vault, so the snapshot can be updated without new shares. Keyra never stores a share; shares are shown once, then copied (clipboard guard) or printed (the Windows print spooler may keep a temporary copy).
- The snapshot holds only the accounts the owner picked, encrypted with the legacy key (AES-256-GCM, whole header as associated data). Unknown versions, ids and tampered files are rejected; nothing is written when it is opened.
- Set up, Choose accounts, Update, Make new shares and Turn off each need the master password (same check and wrong-try count as protected accounts). Putting a protected account in from the details panel needs its Windows Hello check.
- Share bytes and keys are zeroed after use. The share text shown, printed or typed lives in .NET strings until garbage collection, like typed master passwords (T12).
- Limits: two share holders who work together can open the snapshot; snapshot copies already handed out keep working with the old shares after "Make new shares" or "Turn off" until they are deleted; the heir view has the same unlocked-session limits (T11, T12) and screen capture is always blocked.

## Windows features considered
- DPAPI / Credential Manager: not used. They would tie unlock to the Windows account (which this design assumes may be compromised or copied) and add complexity. The vault stays protected only by the master password.
- Windows Hello: used only as the gate for protected accounts while unlocked (see above), never to unlock the vault or to hold a key.
- File permissions: the vault is in the user's profile, which other standard users cannot read by default. This is a secondary layer; encryption is the real protection.

## What Keyra cannot protect against
- Two legacy share holders who work together (they can open the legacy snapshot by design).
- Malware, keyloggers, or anyone controlling your Windows session while the vault is unlocked or while you type the master password.
- Someone using your unlocked PC while Keyra is unlocked.
- A weak master password. Argon2id slows guessing; it does not stop guessing of a short or common password.
- Memory dumps or the page file containing strings from an unlocked session.
- Clipboard readers during the clipboard window.
- Losing the master password, or losing every copy of the vault file.
