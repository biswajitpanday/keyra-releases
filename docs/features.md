# Features

Everything Keyra does, its keys and its limits. To install, see [install](install.md).

## Your accounts
- **Accounts:** website, email or username, password, and optional web address and notes. Under "More details": recovery email, recovery phone, PIN and security questions. Search covers website, email, web address, category and recovery email.
- **Categories and favourites:** a sidebar with All, Favourites, your categories (Personal, Work and Project to start) and No category, each with a count. Star an account to keep it on top.
- **Details panel:** copy the email, password, web address, notes and recovery fields, open the web address in your browser, see when an account was created and when its password last changed. Passwords older than a year are flagged.
- **Trash and multi-select:** deleted accounts stay in Trash for 30 days (restore, delete forever or empty the Trash). Select several accounts with Ctrl-click, Shift-click or Ctrl+A to move them to a category, protect them with Windows Hello, or move them to Trash in one step.
- **Password history:** the last 5 passwords of each account, with show, copy and restore.
- **Health report:** reused, weak and old passwords, problems first, with a tip on how to fix them. It never shows a password.
- **Password generator:** 8–128 characters, character sets of your choice, look-alike characters can be excluded, strength shown in bits.
- **Import:** CSV exports from Chrome, Edge, Firefox, Bitwarden, KeePass, KeePassXC, LastPass, Proton Pass or a simple sheet (UTF-8), including folder or category and favourite columns. Keyra shows how to export from the app you pick, previews what will be added (never a password), and deletes the plaintext CSV after the import. It never reads a browser's password store directly.
- **Backup and restore:** of the encrypted file only. There is no plaintext export. Settings shows when you last backed up, and Keyra reminds you when there has been no backup for 30 days (and something changed since the last one).
- **Legacy access:** give heirs a way in without your master password. Keyra makes three shares; any two of them open a read-only snapshot of the accounts you choose (none by default). See [legacy access](legacy-access.md).

## Protection
- **Encrypted at rest:** Argon2id (256 MiB, 8 passes) derives the key from your master password; AES-256-GCM encrypts the whole vault into one file. See [security](security.md).
- **Offline by design:** Keyra has no network code: no telemetry, no update check. It never sends anything anywhere.
- **Hidden by default:** passwords, notes, the recovery phone, PIN, security answers and old passwords are masked. A revealed value hides again after 20 seconds, when you select another account, when a dialog opens, when the window loses focus, or when the vault locks.
- **Screen-capture blocking:** Keyra's windows are left out of screenshots, recordings and screen sharing (on by default; always on while locked). Optionally blur the list rows you are not pointing at.
- **Windows Hello for chosen accounts:** protected accounts ask for Windows Hello (or the master password) before a secret is shown or copied, or the account is changed. This is an extra gate inside the unlocked app, not extra encryption.
- **Safe clipboard:** copied values are kept out of Windows clipboard history and cloud clipboard and are cleared after 30 seconds (adjustable).
- **Auto-lock:** after inactivity (5 minutes by default), on minimize, when Windows locks, on sleep and on exit. Optionally hide Keyra next to the clock instead of the taskbar (Minimize to tray); it locks when it hides.

## Comfort
- **Calm lock screen:** says what Keyra is (Encrypted · Offline · No tracking) and its version, warns when Caps Lock is on, and helps without alarm after a wrong password.
- **Short tour:** after the first unlock, six tips point at the main parts of the app. Settings → Take the tour again.
- **Light and dark theme:** follows Windows, or choose one. Every setting and its default: [settings](settings.md).

## Keyboard

| Keys | Action |
|---|---|
| Ctrl+F | Search |
| Ctrl+N | Add account |
| Ctrl+G | Password generator |
| Ctrl+L | Lock |
| Enter / click | Open the details panel of the selected account |
| Double-click | Edit the account |
| Space | Show or hide the selected password |
| Ctrl+C / Ctrl+B | Copy password / copy email of the selected account |
| Ctrl-click / Shift-click / Ctrl+A | Select several accounts (in the current view) |
| Del | Move the selected account(s) to Trash (asks first); in Trash, delete forever |
| F2 / Del in the sidebar | Rename / delete the selected category |
| Esc | Close the dialog, panel or tour (skip); in the search box, clear the search; with several accounts selected, clear the selection |
| Tab | Moves through the screen in reading order; dialogs keep the focus inside |

On a protected account, Space, Ctrl+C, Del and double-click ask for Windows Hello first.

## Limitations

- Windows only, single user, one vault. No sync service and no browser autofill.
- While unlocked, account data lives in normal .NET strings, which cannot be reliably wiped from memory.
- The MSIX is signed with a self-signed certificate, so you trust it once by hand (see [install](install.md)).
- Windows Hello protection guards the app, not the file: anyone with the master password can open every account.
- Screen-capture blocking does not stop a camera or software that gets around Windows display affinity.
- A legacy snapshot is a copy made by hand. It does not update itself; Keyra reminds you when it is out of date.
- Minimize to tray hides Keyra only after locking it; the tray icon exists only while Keyra is hidden.
- Updates are installed by hand; Keyra does not tell you when a new version exists.
- If two PCs change the vault at the same time (for example through OneDrive), Keyra does not merge: it keeps the other change and locks; your unsaved change is not made.
