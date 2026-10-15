# Backup and recovery

## Make a backup
Settings → **Back up vault** → choose a place → Save.

- The backup is a byte-for-byte copy of the encrypted vault. It is never plaintext.
- It opens with the master password that was active **when the backup was made**.
- Keep at least one copy off this PC: USB drive, external disk, or another cloud folder.
- If OneDrive backs up your Documents folder, the vault in `Documents\Keyra` is synced too. That is an off-site copy of the *current* vault, but it is not a backup: a mistake (like deleting an account) is synced too. Make real backups as well.
- Make a new backup after changing the master password, and after big changes. Settings shows "Last backup: …", and the status bar reminds you when the last backup is more than 30 days old and the vault changed since (or, if you never backed up, when your oldest account is more than 30 days old).

You can also copy `vault.keyra` by hand while Keyra is closed or locked. It is the same thing. If the vault file was changed outside Keyra (for example by OneDrive from another PC), Keyra does not make the backup: it locks, and after the next unlock you back up the newest version.

## Restore a backup
From the lock screen (**Restore from backup…**) or Settings → **Restore from backup**:

1. Choose the backup file.
2. Enter the master password **of that backup**.
3. Keyra decrypts the backup first. If the password is wrong or the file is damaged, nothing changes.
4. Your current vault is kept next to it as `vault.keyra.pre-restore-<time>.bak`.
5. The backup replaces the vault and Keyra locks. Unlock with the backup's password.

To undo a restore: restore the `.bak` file the same way (it opens with the password that vault had). If no vault was there before, no `.bak` is made.

## If Keyra says the vault is damaged
"The vault could not be authenticated or appears corrupted. Your original vault has not been modified." (damaged header or content), or "Wrong password. Try again." and, from the second try, a help line that also names a damaged file (a wrong password and a modified file look the same).

If it says "The vault file could not be read right now", the file is only busy or not downloaded yet (OneDrive). Wait until it is downloaded, then press Unlock again. Do not restore in that case.

If it says "The vault file was changed outside Keyra", another PC or program saved the vault while Keyra was open. Keyra did not write over it and locked; unlock again to see the newest version. "Moved or deleted outside Keyra" means the file is gone: put it back in `Documents\Keyra`, or restore a backup.

- Keyra never overwrites or replaces a vault it cannot open.
- Check you typed the right master password (a wrong password and a damaged file look the same).
- If OneDrive shows a conflict copy (`vault-<PC name>.keyra`), try restoring that copy.
- Otherwise restore your latest backup.

## Lost master password
There is no recovery. Nobody, including Keyra, can open the vault or its backups without it.
If you still remember an **older** password, a backup made while that password was active will open with it.
If you set up legacy access, any two of the three shares open the last legacy snapshot (**Open legacy vault…** on the lock screen). It is read-only and holds only the accounts you chose, so it is not a backup. See [legacy-access.md](legacy-access.md).

## Moving to a new PC
Install Keyra 1.5 or newer (Windows 10 2004 or later), copy `vault.keyra` into `Documents\Keyra` (or use Restore), unlock with your master password.

## Importing from a CSV (one time)
**Import** in the command bar (or **Import CSV** on an empty vault) opens one dialog with three steps. 1) Pick where your passwords are now (Chrome, Edge, Firefox, Bitwarden, KeePass, KeePassXC, LastPass, Proton Pass or another CSV) to see how to export them, then **Choose CSV file…**. 2) Keyra shows how many accounts are new, how many are already in Keyra (same website + email) and which lines are not usable (missing website, email or password, or a field too long), plus the first new websites; "Delete the CSV file after the import" is ticked. 3) **Import** saves once and deletes the CSV. URL, notes, folder and favourite columns are imported too; a URL that is not a normal web address is left out. The file must be UTF-8: in Excel use "CSV UTF-8 (Comma delimited)". Other encodings are rejected so that no character in a password is changed.
Deleting is not a secure wipe: also empty the Recycle Bin and remove old copies (for example from OneDrive version history).
