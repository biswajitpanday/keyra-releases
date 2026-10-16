# Features

Everything Keyra does, its keys and its limits. To install, see [install](install.md).

## Your accounts
- **Item types:** logins, cards, bank accounts, secure notes and identities in one vault. "Add item" adds a login; its small arrow opens a menu of the types. The type of an item cannot change later.
- **Logins:** website, email or username, password, and optional web address and notes. Under "More details": recovery email, recovery phone, PIN and security questions.
- **Cards:** cardholder, number, brand (set from the number), valid from, expiry, CVV, PIN, billing address and bank phone. A number that fails the card check gets a warning but can still be saved.
- **Bank accounts:** bank, account holder, account number, type, IBAN (checked, warning only), SWIFT/BIC, routing or sort code, branch, branch address, bank phone and PIN.
- **Secure notes and identities:** notes of up to 50 000 characters; identities with title, full name, company, date of birth, email, phone, address, username, passport, national ID and driver licence (each with an expiry date) and tax number.
- **Search:** names, emails, web addresses, categories, recovery email and the plain details of cards, bank accounts and identities (cardholder, brand, bank, account holder, full name, company). Never numbers or secrets.
- **Types, categories and favourites:** a sidebar with All items, Favourites, the types (Logins, Cards, Bank accounts, Secure notes, Identities), your categories (Personal, Work and Project to start) and No category, each with a count. Star an item to keep it on top. Rows show the type and a short summary such as "Visa ···· 1111 · exp 08/28"; only the last 4 digits are ever shown.
- **Details panel:** every field of the item with copy buttons, secrets masked with show and copy; open a login's web address in your browser; see when an item was created and changed and when a password last changed. Passwords older than a year are flagged.
- **Trash and multi-select:** deleted items stay in Trash for 30 days (restore, delete forever or empty the Trash). Select several items with Ctrl-click, Shift-click or Ctrl+A to move them to a category, protect them with Windows Hello, or move them to Trash in one step.
- **Password history:** the last 5 passwords of each login, with show, copy and restore.
- **Health report:** reused, weak and old login passwords, and an Expiring tab for cards (60 days before the end of the expiry month), passports (180 days), ID cards and driver licences (60 days). Problems first, with a tip on how to fix them. It never shows a password or number.
- **Password generator:** 8–128 characters, character sets of your choice, look-alike characters can be excluded, strength shown in bits.
- **Import:** CSV exports from Chrome, Edge, Firefox, Bitwarden, KeePass, KeePassXC, LastPass, Proton Pass or a simple sheet (UTF-8), including folder or category and favourite columns. Keyra shows how to export from the app you pick, previews what will be added (never a password), and deletes the plaintext CSV after the import. Rows with a name and notes but no username and no password become secure notes. It never reads a browser's password store directly.
- **Backup and restore:** of the encrypted file only. There is no plaintext export. Settings shows when you last backed up, and Keyra reminds you when there has been no backup for 30 days (and something changed since the last one).
- **Legacy access:** give heirs a way in without your master password. Keyra makes three shares; any two of them open a read-only snapshot of the items you choose (none by default). See [legacy access](legacy-access.md).

## Protection
- **Encrypted at rest:** Argon2id (256 MiB, 8 passes) derives the key from your master password; AES-256-GCM encrypts the whole vault into one file. See [security](security.md).
- **Offline by design:** Keyra has no network code: no telemetry, no update check. It never sends anything anywhere.
- **Hidden by default:** passwords, notes, the recovery phone, PINs, security answers, old passwords, card numbers, CVVs, account numbers, IBANs and ID numbers are masked. A revealed value hides again after 20 seconds, when you select another item, when a dialog opens, when the window loses focus, or when the vault locks.
- **Screen-capture blocking:** Keyra's windows are left out of screenshots, recordings and screen sharing (on by default; always on while locked). Optionally blur the list rows you are not pointing at.
- **Windows Hello for chosen items:** protected items ask for Windows Hello (or the master password) before a secret is shown or copied, or the item is changed. New cards, bank accounts and identities start protected. This is an extra gate inside the unlocked app, not extra encryption.
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
| Ctrl+N | Add item (a login, or the type of the current type view) |
| Ctrl+G | Password generator |
| Ctrl+L | Lock |
| Enter / click | Open the details panel of the selected item |
| Double-click | Edit the item |
| Space | Show or hide the main secret (password, card number, account number, note, ID number) |
| Ctrl+C / Ctrl+B | Copy the main secret / copy the email, cardholder, account holder or full name |
| Ctrl-click / Shift-click / Ctrl+A | Select several items (in the current view) |
| Del | Move the selected item(s) to Trash (asks first); in Trash, delete forever |
| F2 / Del in the sidebar | Rename / delete the selected category |
| Esc | Close the dialog, panel or tour (skip); in the search box, clear the search; with several items selected, clear the selection |
| Tab | Moves through the screen in reading order; dialogs keep the focus inside |

On a protected item, Space, Ctrl+C, Del and double-click ask for Windows Hello first; Ctrl+B copies a plain value and never asks.

## Limitations

- Windows only, single user, one vault. No sync service and no browser autofill.
- While unlocked, item data lives in normal .NET strings, which cannot be reliably wiped from memory.
- The MSIX is signed with a self-signed certificate, so you trust it once by hand (see [install](install.md)).
- Windows Hello protection guards the app, not the file: anyone with the master password can open every item.
- Screen-capture blocking does not stop a camera or software that gets around Windows display affinity.
- A legacy snapshot is a copy made by hand. It does not update itself; Keyra reminds you when it is out of date.
- Minimize to tray hides Keyra only after locking it; the tray icon exists only while Keyra is hidden.
- Updates are installed by hand; Keyra does not tell you when a new version exists.
- Keyra 1.5 cannot open a vault saved by 1.6. To go back, uninstall 1.6 and rename the copy `vault.keyra.pre-1.6.bak` (made before the first 1.6 save) to `vault.keyra`; see [the FAQ](faq.md#can-i-go-back-to-keyra-15).
- No browser autofill, no 2FA codes and no attachments yet.
- If two PCs change the vault at the same time (for example through OneDrive), Keyra does not merge: it keeps the other change and locks; your unsaved change is not made.
