# Install, update and uninstall

Keyra needs Windows 10 version 2004 (build 19041) or later, x64. It installs as an MSIX package signed with a self-signed certificate (subject `CN=Keyra Dev`).

## Install

1. From [Releases](https://github.com/biswajitpanday/keyra-releases/releases), download `Keyra_<version>_x64.msix` and `KeyraDev.cer`.
2. Check the download. In PowerShell, in the download folder (replace `<version>` with the name of the file you downloaded, e.g. `1.5.0.0`):
   `Get-FileHash .\Keyra_<version>_x64.msix -Algorithm SHA256` must match the SHA-256 on the release page. This shows the file is complete; it cannot prove who made it.
3. Check the signer:
   `(Get-AuthenticodeSignature .\Keyra_<version>_x64.msix).SignerCertificate.Thumbprint` and `(Get-PfxCertificate .\KeyraDev.cer).Thumbprint` must both be
   `AA4E8EA05917E2598282E8120D44F17A1178D97D`. If either differs, stop and do not install. This catches a swapped file or a different certificate. It cannot help if this GitHub account itself is taken over.
4. Once, in an **admin** PowerShell in the download folder, trust the certificate:
   `Import-Certificate -FilePath '.\KeyraDev.cer' -CertStoreLocation Cert:\LocalMachine\TrustedPeople`
   This trusts every package signed with this certificate on this PC, so only do it if you trust the publisher. Use `TrustedPeople`, never `Root`.
5. Install: `Add-AppxPackage -Path '.\Keyra_<version>_x64.msix'` (or double-click the file), then start **Keyra** from the Start menu.

The first start asks you to create a master password. Keyra cannot recover it. Make a backup right after (Settings → Back up vault).

## Update

Keyra never checks for updates by itself. To update:

1. Make a backup (Settings → Back up vault).
2. Download the new version and check its SHA-256 and signer as above.
3. Close Keyra and run `Add-AppxPackage` with the new MSIX. It upgrades in place; your vault in `Documents\Keyra` is not touched.

To get notified, use **Watch → Custom → Releases** on this repository.

## Uninstall and remove data

Before you uninstall: if "Unlock with Windows Hello" is on, turn it off in Settings → Security. This deletes Keyra's Windows Hello key.

1. Settings → Apps → Keyra → Uninstall. This removes the app and, usually, its theme choice and event log. Your vault in the Documents folder is **not** removed.
2. To remove the certificate trust, in an **admin** PowerShell:
   `Get-ChildItem Cert:\LocalMachine\TrustedPeople | Where-Object Thumbprint -eq 'AA4E8EA05917E2598282E8120D44F17A1178D97D' | Remove-Item`
   (or `certlm.msc` → Trusted People → Certificates → `Keyra Dev` → Delete).
3. To remove your data, delete `Documents\Keyra` (the vault and its old `.bak` copies) and your own backups and legacy snapshot files. This cannot be undone. If Documents is in OneDrive, also check the OneDrive recycle bin and version history.
4. If `%LOCALAPPDATA%\Keyra` exists, delete it too.

## Where things are

| What | Where |
|---|---|
| Vault (encrypted) | `Keyra\vault.keyra` in your Documents folder: usually `%USERPROFILE%\Documents`, or `%USERPROFILE%\OneDrive\Documents` when OneDrive backs up Documents. If Documents is synced, the encrypted file syncs too. |
| Old copies: the vault before a restore, and your Keyra 1.5 vault (they open with the master password of that time; delete them in Settings › Backup) | same folder, `vault.keyra.pre-restore-<time>.bak` and `vault.keyra.pre-1.6.bak` |
| Legacy snapshot (encrypted, only if you set up legacy access) | the file you choose |
| Theme choice and event log (no secrets) | `%LOCALAPPDATA%\Keyra\`, or the MSIX private copy of it |
