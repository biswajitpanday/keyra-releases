# FAQ and troubleshooting

## I forgot my master password
Nobody can open the vault without it, including Keyra. If you remember an older password, a backup made while it was active opens with it. If you set up legacy access, two of the three shares open your last legacy snapshot (read-only, only the chosen items). See [backup and recovery](backup-and-recovery.md).

## "Wrong password. Try again."
Check Caps Lock (Keyra shows "Caps Lock is on") and the keyboard language. From the second try Keyra adds a help line: a wrong password and a damaged file look the same, so if you are sure the password is right, restore a backup.

## "The vault file could not be read right now"
The file is busy or not downloaded (OneDrive Files On-Demand). In File Explorer, right-click `Documents\Keyra\vault.keyra` → **Always keep on this device**, wait until it is downloaded, then unlock again. Do not restore in this case.

## "The vault file was changed outside Keyra"
Another PC, OneDrive or another program saved the vault while Keyra was open. Keyra did not write over it and locked. Unlock again to see the newest version, then redo your last change. To avoid it, use one PC at a time and let OneDrive finish syncing first.

## "The vault file was moved or deleted outside Keyra"
The file is gone from `Documents\Keyra`. Put it back (OneDrive recycle bin, File Explorer), or restore a backup.

## OneDrive made a copy like `vault-<PC name>.keyra`
That is a OneDrive conflict copy. Keep Keyra's file as it is, and if it misses a change, restore the conflict copy with **Restore from backup** (it asks for that copy's master password).

## "This vault was saved by a newer version of Keyra"
Install the newest Keyra from the releases page. Keyra never writes over a vault from a newer version.

## "This vault was made by an early test version of Keyra"
Only files from early test versions show this. Keyra cannot open them and does not change them.

## What is `vault.keyra.pre-1.6.bak`?
The copy of your vault from Keyra 1.5. Keyra 1.6 makes it once, right before it saves your vault in the new format for the first time. It is encrypted with the master password you had then. Keep it until you are sure you will not go back to 1.5; then you may delete it.

## Can I go back to Keyra 1.5?
Yes, with the copy Keyra 1.6 kept, but changes made in 1.6 are not in it, and 1.5 knows no cards, bank accounts, notes or identities. Keyra 1.5 cannot open or restore over a vault saved by 1.6, so swap the files by hand:
1. Close Keyra and uninstall 1.6 (Settings → Apps → Keyra → Uninstall). Your vault in `Documents\Keyra` stays.
2. In `Documents\Keyra`, rename `vault.keyra` to `vault.keyra.1.6.bak` and keep it.
3. Rename `vault.keyra.pre-1.6.bak` to `vault.keyra`.
4. Install 1.5 and unlock with the master password you had then.

## "Keyra could not make a copy of your vault before the upgrade"
Keyra did not save anything, so your vault is unchanged. Check that `Documents\Keyra` is not full or read-only and that OneDrive has finished syncing, then try again.

## Which item types does Keyra have?
Logins, cards, bank accounts, secure notes and identities. Choose the type with the small arrow next to "Add item". The type of an item cannot be changed later.

## Windows Hello does not appear
Windows Hello must be set up in Windows Settings → Accounts → Sign-in options. Without it Keyra asks for the master password instead. Windows Hello is only a gate for protected items inside the unlocked app; it never unlocks the vault.

## How do I move Keyra to a new PC?
Install Keyra, copy `vault.keyra` into `Documents\Keyra` on the new PC (or use **Restore from backup**), and unlock with your master password.

## Can I keep the vault somewhere else?
The installed app always uses `Documents\Keyra\vault.keyra`; another location is not supported. If Documents is in OneDrive, the encrypted file is synced with it.

## Keyra looks blurry on my second screen
Keyra is per-monitor DPI aware, so it should stay sharp on every screen and when you move it between screens. If it does not, please report it with your screen scales.

## Why can't I take a screenshot of Keyra?
"Block screenshots" is on by default. Turn it off in Settings → Advanced → Privacy (it asks first). It is always on before unlock.

## Does Keyra update itself?
No. It never connects to the internet. Watch the releases page and install new versions by hand (see [update](install.md#update)).

## Where is my data, and how do I remove it?
See [privacy](privacy.md) and [uninstall and remove data](install.md#uninstall-and-remove-data).

## How do I report a bug or a security problem?
Bugs and ideas: the issue forms on the releases repository (never put real passwords or vault files in an issue). Security problems: report them privately, see [SECURITY.md](../SECURITY.md).
