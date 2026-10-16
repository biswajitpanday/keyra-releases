# Settings

Open **Settings** in the command bar. Every setting except the theme is stored inside the encrypted vault, so it moves with the vault file and cannot be changed by editing a plain file.

## Security

| Setting | Default | Choices | What it does |
|---|---|---|---|
| Auto-lock | 5 minutes | 1, 5, 10, 15 minutes, Never | Locks after that long without keyboard or mouse use in Keyra. |
| Clear clipboard | 30 seconds | 10 s, 30 s, 1 min, 2 min | Clears a copied password or email after that time, only if you copied nothing else since. |
| Lock on minimize | On | On / Off | Locks when Keyra is minimized to the taskbar. |
| Minimize to tray | Off | On / Off | Minimize locks Keyra and hides it next to the clock instead of the taskbar. |
| Lock when Windows locks | On | On / Off | Locks on Windows lock, user switch and remote disconnect. |
| Change master password | — | — | Re-encrypts the whole vault with a new key. Old backups keep the old password. |

Keyra also always locks on sleep, on exit, and after 5 wrong master passwords in a row in a "Confirm it's you" check.

## Backup

| Item | What it does |
|---|---|
| Back up vault | Saves a copy of the encrypted vault where you choose. Shows "Last backup: …", and "A copy for Keyra 1.5 is kept as <file name>." while a pre-1.6 copy exists. |
| Restore from backup | Replaces the vault with a backup (asks for the backup's master password; keeps the old vault as `.bak`). |
| Vault file | Shows where the vault is; **Open folder** opens it in File Explorer. |

## Appearance

| Setting | Default | Choices | What it does |
|---|---|---|---|
| Theme | System | System, Light, Dark | System follows the Windows app theme. Stored in `theme.txt` (not secret). |
| Take the tour again | — | — | Shows the six short tips once more. |

## Advanced

Closed when Settings opens. It opens by itself when your legacy snapshot is out of date.

### Privacy

| Setting | Default | What it does |
|---|---|---|
| Block screenshots | On | Keyra's windows are left out of screenshots, recordings and screen sharing. Turning it off asks first. It is always on before unlock. |
| Blur the list | Off | Blurs website and email of the rows you are not pointing at or have selected. |

### Legacy access

| Item | What it does |
|---|---|
| Set up legacy access | Makes three shares; any two open a read-only snapshot of the items you choose. |
| Choose items | Picks the items that go into the snapshot (none by default). |
| Update snapshot | Writes the snapshot again with the current items, same shares. |
| Make new shares | New key and shares; the old shares stop working for new snapshots. |
| Turn off | Removes legacy access and offers to delete the snapshot file. |

Each legacy action asks for your master password. Details: [legacy-access.md](legacy-access.md).

## Not in Settings

- The vault location is fixed (`Documents\Keyra\vault.keyra`).
- Keyra has no update check, no telemetry and no network setting, because it never connects to the internet.
