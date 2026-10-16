# Release notes

## 1.6.0

Keyra now keeps more than logins: cards, bank accounts, secure notes and identities, in the same encrypted vault.

### New
- Item types: **Login**, **Card** (cardholder, number, brand, valid from, expiry, CVV, PIN, billing address, bank phone), **Bank account** (bank, holder, account number, type, IBAN, SWIFT/BIC, routing or sort code, branch, PIN), **Secure note** (up to 50 000 characters) and **Identity** (title, name, company, date of birth, contact details, passport, national ID and driver licence with expiry dates, tax number).
- A "Types" group in the sidebar with counts, and "Add item" with a menu of the types.
- Rows show the type and a short summary, like "Visa ···· 1111 · exp 08/28"; only the last 4 digits are ever shown. Card numbers, CVVs, PINs, account numbers, IBANs, note text and ID numbers are masked like passwords.
- New cards, bank accounts, secure notes and identities start protected with Windows Hello.
- The editor checks each field when you leave it and on Save, ignores characters a field cannot hold (also when you paste), and shows month and year like "08/28".
- Health has a new **Expiring** tab: cards (60 days before the end of the expiry month), passports (180 days) and ID cards and driver licences (60 days).
- CSV import brings in secure notes (rows with a name and notes, but no username, password or web address).
- Settings › Backup shows your old copies of the vault (from before a restore, and your 1.5 vault). It warns that they still open with the master password of that time, and deletes them after asking.
- Search also finds cardholders, brands, banks, account holders, names and companies, never numbers or secrets.

### Upgrade from 1.5
- Keyra 1.6 saves your vault in a new way that Keyra 1.5 cannot open (1.5 says it is from a newer Keyra and changes nothing). Before the first save, which can come right after the first unlock, Keyra keeps a copy of your 1.5 vault as `vault.keyra.pre-1.6.bak` next to it. Going back to 1.5 means losing everything done in 1.6; follow the steps in the FAQ.
- New legacy snapshots need Keyra 1.6 or newer to open. Your existing shares keep working.

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
Windows 10 version 2004 (build 19041) or later, x64. See [install](install.md): check the SHA-256 and the signer thumbprint `AA4E8EA05917E2598282E8120D44F17A1178D97D` (subject `CN=Keyra Dev`), trust the certificate once, then `Add-AppxPackage`.

### Known limits
- Windows only, one user, one vault; no sync service, no browser autofill, no 2FA codes yet.
- While unlocked, account data lives in normal .NET strings that cannot be wiped reliably.
- The MSIX is signed with a self-signed certificate; trusting it means trusting its publisher.
- Updates are installed by hand.
