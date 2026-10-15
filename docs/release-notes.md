# Release notes

## 1.5.0

The first public release of Keyra: a local, offline password vault for Windows. One encrypted file, no account, no cloud, no telemetry.

### What Keyra does
- Accounts with website, email or username, password, web address, notes, recovery email and phone, PIN, security questions and the last 5 passwords.
- Categories, favourites, search, a details panel, Trash (30 days) and multi-select.
- Health report (reused, weak and old passwords), password generator, CSV import from Chrome, Edge, Firefox, Bitwarden, KeePass, KeePassXC, LastPass and Proton Pass.
- Windows Hello for chosen accounts, screen-capture blocking, a safe clipboard, auto-lock and minimize to tray.
- Encrypted backup and restore with a backup reminder; legacy access for heirs (any two of three shares open a read-only snapshot of the accounts you choose).
- Light and dark theme, sharp on every screen (per-monitor DPI).

### Safety
- Argon2id (256 MiB, 8 passes) and AES-256-GCM; the whole file is authenticated.
- If another PC, OneDrive or another program changes the vault while Keyra is open, Keyra does not write over it: the next save locks instead, and the next unlock shows the newest version. Only a change in the same instant as a save can slip through.
- Atomic saves; a read-only vault file fails at once instead of waiting.

### Install
Windows 10 version 2004 (build 19041) or later, x64. See "Install" in the README: check the SHA-256 and the signer thumbprint `AA4E8EA05917E2598282E8120D44F17A1178D97D` (subject `CN=Keyra Dev`), trust the certificate once, then `Add-AppxPackage`.

### Known limits
- Windows only, one user, one vault; no sync service, no browser autofill, no 2FA codes yet.
- While unlocked, account data lives in normal .NET strings that cannot be wiped reliably.
- The MSIX is signed with a self-signed certificate; trusting it means trusting its publisher.
- Updates are installed by hand.
