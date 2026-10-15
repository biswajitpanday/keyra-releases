# Privacy

Keyra is an offline app. It has no network code, no account, no telemetry and no update check. It never sends anything anywhere, and nobody, including the author, can see your data.

## What Keyra stores, and where

| What | Where | Readable by others? |
|---|---|---|
| Your accounts, categories, settings and (if set up) the legacy key | `Documents\Keyra\vault.keyra` | No. Encrypted with a key from your master password (Argon2id + AES-256-GCM). |
| The previous vault after a restore | `Documents\Keyra\vault.keyra.pre-restore-<time>.bak` | No. Encrypted the same way. |
| Backups and legacy snapshots | Where you save them | No. Encrypted. |
| Theme choice (System, Light or Dark) | `theme.txt` in Keyra's app data folder | Yes, but it holds only the theme. |
| Event log | `keyra.log` in Keyra's app data folder | Yes, but it holds only event names and error codes: never passwords, keys, account data or error messages. |

Keyra's app data folder is `%LOCALAPPDATA%\Keyra`. The installed app usually keeps a private copy of it that Windows removes on uninstall; if the real folder already existed, Windows uses that one and leaves it.

## What Keyra never stores

- Your master password or the vault key.
- Any account data in plain text: there is no export, no plain temp file and no plain backup.
- Copied passwords after the clipboard time: Keyra clears them unless you copied something else since, and keeps them out of Windows clipboard history and cloud clipboard (nothing can clear them if Keyra is killed or the PC loses power).

## Things that happen outside Keyra

- If your Documents folder is backed up by OneDrive, OneDrive copies the **encrypted** vault file to the cloud. Keyra does not talk to OneDrive.
- **Open** next to a web address hands that address to your browser after you click it. That is the browser's traffic, not Keyra's.
- A CSV file you import holds your passwords in plain text. Keyra offers to delete it, but cannot wipe every copy (recycle bin, OneDrive versions).
- Legacy shares that you print go through the Windows print spooler; a PDF or OneNote printer saves them in a file.

## Your control

- All your data is in files you can see, copy and delete. See "Uninstall and remove data" in the [README](../README.md).
- Keyra cannot recover a lost master password. Nobody can.

More: [security](security.md), [threat model](threat-model.md).
