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

## What are the old copies (`vault.keyra.pre-1.6.bak`, `vault.keyra.pre-restore-….bak`)?
Keyra keeps these copies of your vault next to it:
- `vault.keyra.pre-1.6.bak`: your vault from Keyra 1.5, made once, right before 1.6 saves the new format for the first time.
- `vault.keyra.pre-restore-<time>.bak`: your vault from just before a restore.

Each copy is encrypted, but it opens with the master password you had **when it was made**, and it still has the items of that day, also the ones you deleted later. If Documents syncs to OneDrive, the copies sync too. Keep them only while you may go back to Keyra 1.5 or want that older vault. To delete them, open Settings › Backup › **Delete old copies**. If you changed your master password because someone may know the old one, delete them now. Then also empty the OneDrive recycle bin.

## Can I go back to Keyra 1.5?
Yes, with `vault.keyra.pre-1.6.bak`, but you lose everything you did in 1.6 or later:
- every item added since, also logins;
- every change, deletion and category change made since;
- a new master password (the copy opens with the one you had then).

Keyra 1.5 cannot open a vault saved by 1.6 or later, so swap the files by hand:
1. If "Unlock with Windows Hello" is on, turn it off in Settings → Security. Close Keyra and uninstall it (Settings → Apps → Keyra → Uninstall). Your vault in `Documents\Keyra` stays.
2. In `Documents\Keyra`, rename `vault.keyra` to `vault.keyra.1.6.bak` and keep it. Only Keyra 1.6 or newer can open it.
3. Rename the 1.5 copy to `vault.keyra`. It is `vault.keyra.pre-1.6.bak`, or the newest `vault.keyra.pre-1.6-<time>.bak` if there are several.
4. Install 1.5 and unlock with the master password you had then.

If you deleted the old copies (Settings › Backup › Delete old copies), you cannot go back to 1.5.

## Can I go back to Keyra 1.6?
Yes, and you keep everything: 1.7 saves the vault the same way as 1.6. If "Unlock with Windows Hello" is on, turn it off in Settings → Security, uninstall Keyra (Windows does not install an older version over a newer one), then install 1.6. Your vault in `Documents\Keyra` stays and opens as it is.

## "Keyra could not make a copy of your vault before the upgrade"
Keyra did not save anything, so your vault is unchanged. Check that `Documents\Keyra` is not full or read-only and that OneDrive has finished syncing, then try again.

## Which item types does Keyra have?
Logins, cards, bank accounts, secure notes and identities. Choose the type with the small arrow next to "Add item". The type of an item cannot be changed later. New cards, bank accounts, secure notes and identities start protected with Windows Hello; you can untick it before you save.

## Should I save my card's CVV?
It is your choice. Keyra keeps the CVV encrypted and masked, behind Windows Hello on a protected card. But anyone with your master password can see it, and it stays in your backups and old copies. Your bank may also ask you not to write it down anywhere. If you only shop with a few cards, you may prefer to leave the CVV out and read it from the card.

## Windows Hello does not appear
Windows Hello must be set up in Windows Settings → Accounts → Sign-in options. Without it Keyra asks for the master password instead. For "Unlock with Windows Hello", the switch in Settings → Security is disabled until Windows Hello is set up (if it is already on, you can still turn it off).

## How does "Unlock with Windows Hello" work?
Turn it on in Settings → Security (it asks your master password once). After that, each time you unlock with your master password, Windows Hello asks once to confirm. When Keyra locks, the lock screen then asks Windows Hello by itself when you bring Keyra to the front, and shows an "Unlock with Windows Hello" button. It works only while Keyra runs: after you close Keyra or restart the PC, and at least every 14 days, you need your master password. Protected items still ask for their own check. The setting is per PC.

## Keyra says "Use your master password this time"
Windows Hello unlock could not open the vault, for example in a Remote Desktop session, after the Windows Hello key was removed, or after a restore or a master password change on another PC. Unlock with your master password; Hello unlock starts again after that.

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
