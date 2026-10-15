# Threat model

Assets: account passwords, emails, website list, recovery data (recovery email and phone, PIN, security answers), password history, master password, and the legacy key, the three legacy shares and the legacy snapshot.
Trust boundary: the user's unlocked Windows session. Everything outside it (files, backups, cloud copies, source, binaries) is assumed visible to an attacker.

| # | Threat | Result | Why |
|---|---|---|---|
| T1 | Someone copies `vault.keyra` (disk, backup, OneDrive, USB) | Cannot read it without the master password | AES-256-GCM; key only from Argon2id(master password) |
| T2 | Offline guessing of the master password | Slow: ~0.85 s and 256 MiB per guess on the development PC; still possible for weak passwords | Argon2id; strength hint on setup; a Weak password needs an extra "I understand" tick; weak KDF params upgraded after unlock |
| T3 | Someone modifies the vault (bytes, header, KDF params, nonce, truncation) | Detected; no data shown; vault not overwritten; restore offered | GCM tag over payload, header as associated data, bounds checks before KDF |
| T4 | Someone swaps in an older vault file | Opens if the old password is used; shows old data | Not prevented. Same as restoring a backup. Accepted. A file changed while Keyra is open (another PC, OneDrive) is never overwritten: the next save, backup or upgrade locks instead; only a change in the instant between the last check and the rename is missed |
| T5 | Crash or power loss during save | Old vault intact | Temp file + flush + atomic rename |
| T6 | Attacker has source code or binaries | No help | No hard-coded keys or salts |
| T7 | Attacker reads ordinary config/log files | Nothing secret | Settings with security effect are inside the vault; log has no secrets |
| T8 | Shoulder surfing | Reduced | Masked by default, fixed-length mask, timed re-hide, hide when a dialog opens; optional blur of the list rows |
| T9 | Clipboard leakage | Reduced | History/cloud exclusion, timed clear only if unchanged, clear on lock |
| T10 | Unlocked app left alone | Reduced | Auto-lock 5 min, lock on minimize/Windows lock/suspend; minimize to tray always locks before hiding |
| T11 | Someone uses the unlocked session | **Not protected.** They can see what the app shows. | Out of scope by design |
| T12 | Malware or admin controls the Windows account | **Not protected.** Can read memory, keystrokes, clipboard. | Keyra does not protect against a compromised OS |
| T13 | Plaintext copies (export, temp files, crash dumps) | No export; temp files are encrypted; Keyra writes no plaintext itself. Exception by the user's choice: legacy shares printed to a PDF or OneNote printer end up in a file (the wizard warns) | Crash dumps made by Windows may contain memory of an unlocked session |
| T14 | CSV import file left on disk | User warned and offered deletion; no secure wipe | Plaintext file was created by the user |
| T15 | Two Keyra instances overwrite each other | Prevented | Single-instance mutex; a second start only asks the running Keyra to show itself (session-local named event, no data); another program can at most bring the Keyra window forward (the lock screen when it was hidden in the tray) |
| T16 | Lost master password | Data lost | No recovery by design. If legacy access was set up, two shares open the last snapshot of the chosen accounts, read-only; not a recovery of the vault |
| T17 | Screenshot, recording or screen share of an unlocked vault | Keyra windows excluded from capture by default; blur the list option | Camera, memory readers, low-level remote desktop |
| T18 | Someone at the unlocked PC opens a sensitive account | Reduced for protected accounts: secrets and changes need Windows Hello or the master password; 60 s per account; 5 wrong master passwords (also as the current password in Change master password) lock the vault | Gate only, not encryption: memory readers (T12) and unprotected accounts are not covered |
| T19 | Legacy shares or snapshot are found or stolen | One share reveals nothing; the snapshot alone reveals no account data (only its set id, snapshot time and rough size); two shares and the snapshot open only the picked accounts, read-only | Two share holders working together; old snapshot copies keep working with old shares until deleted |
| T20 | Legacy snapshot is tampered with or replaced | Header is associated data, wraps and payload are authenticated; unknown versions rejected; opening writes nothing | A thief who has two shares can read it |
| T21 | Tampered or fake update package | Reduced | The SHA-256 on the release page only catches a broken download (MSIX, `.cer` and hash come from the same page). The signer thumbprint (README, SECURITY.md, release page) catches a fake release with its own certificate; all copies are under the same GitHub account, so it does not help if that account is taken over. Signatures are timestamped and stay valid after the certificate expires. Users still trust the publisher of a self-signed certificate |
