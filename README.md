<p align="center">
  <img src="docs/images/logo.svg" width="96" height="96" alt="Keyra logo">
</p>

<h1 align="center">Keyra</h1>

<p align="center">
  <b>A small, local, offline password vault for Windows.</b><br>
  One encrypted file. No account, no cloud, no telemetry.
</p>

<p align="center">
  <a href="https://github.com/biswajitpanday/keyra-releases/releases/latest"><img src="https://img.shields.io/github/v/release/biswajitpanday/keyra-releases?label=download&color=7c3aed" alt="Latest release"></a>
  <img src="https://img.shields.io/badge/Windows-10%20%7C%2011-0078D4" alt="Windows 10 and 11">
  <img src="https://img.shields.io/badge/network-none-2ea44f" alt="No network">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-PolyForm%20Noncommercial-f43f8e" alt="License: PolyForm Noncommercial"></a>
</p>

<p align="center">
  <img src="docs/images/details-dark.png" width="820" alt="Keyra in dark theme: a card with its details panel open">
</p>

## Why Keyra

<table>
  <tr>
    <td width="50%"><b>🔒 Strong encryption</b><br>Argon2id (256 MiB, 8 passes) and AES-256-GCM. Any changed byte makes the file refuse to open.</td>
    <td width="50%"><b>📴 Truly offline</b><br>No network code at all: no telemetry, no update check, no cloud.</td>
  </tr>
  <tr>
    <td><b>🗂️ More than passwords</b><br>Logins, cards, bank accounts, secure notes and identities in one encrypted vault.</td>
    <td><b>🩺 Health check</b><br>Finds reused, weak and old passwords, and cards or ID documents that expire soon.</td>
  </tr>
  <tr>
    <td><b>🙈 Private on screen</b><br>Masked values, safe clipboard, auto-lock, hidden from screenshots and screen sharing (on by default).</td>
    <td><b>🗝️ Legacy access</b><br>Any two of three shares open a read-only snapshot of the items you choose.</td>
  </tr>
  <tr>
    <td><b>📥 Easy import</b><br>From Chrome, Edge, Firefox, Bitwarden, KeePass, LastPass, Proton Pass and more.</td>
    <td><b>🔐 Windows Hello</b><br>Chosen items ask for Windows Hello before a secret is shown or copied.</td>
  </tr>
</table>

<p align="center">
  <img src="docs/images/vault-light.png" width="400" alt="Item list in light theme with categories, favourites and protected items">
  &nbsp;
  <img src="docs/images/health-dark.png" width="400" alt="Password health panel in dark theme: an expiring passport">
</p>

All features, keys and limits: [features](docs/features.md).

## Download

1. Download `Keyra_<version>_x64.msix` and `KeyraDev.cer` from the [latest release](https://github.com/biswajitpanday/keyra-releases/releases/latest).
2. Check the file and its signer (below), then trust the certificate once.
3. Double-click the MSIX and start **Keyra** from the Start menu.

<details>
<summary><b>Check the download and trust the certificate</b> (PowerShell)</summary>

```powershell
# Replace <version> with the name of the file you downloaded, e.g. 1.5.0.0
# SHA-256 must match the release page (shows the file is complete, not who made it)
Get-FileHash .\Keyra_<version>_x64.msix -Algorithm SHA256

# Both must be AA4E8EA05917E2598282E8120D44F17A1178D97D, otherwise do not install
(Get-AuthenticodeSignature .\Keyra_<version>_x64.msix).SignerCertificate.Thumbprint
(Get-PfxCertificate .\KeyraDev.cer).Thumbprint

# Once, in an admin PowerShell in the download folder (TrustedPeople, never Root)
Import-Certificate -FilePath '.\KeyraDev.cer' -CertStoreLocation Cert:\LocalMachine\TrustedPeople
```

Trusting the certificate trusts every package signed with it, so only do it if you trust the publisher. Why each check matters: [install guide](docs/install.md#install).
</details>

Needs Windows 10 version 2004 (build 19041) or later, x64. Update, uninstall and where your files are: [install guide](docs/install.md).

> [!IMPORTANT]
> Keyra cannot recover a forgotten master password, and nobody else can either. Make a backup right after setup (Settings → Back up vault) and keep backups somewhere safe. Legacy access is not a recovery tool.

## Documentation

| | |
|---|---|
| [Features](docs/features.md) · [Settings](docs/settings.md) · [FAQ](docs/faq.md) | What Keyra does and how to use it |
| [Install](docs/install.md) · [Backup and recovery](docs/backup-and-recovery.md) · [Legacy access](docs/legacy-access.md) | Keep Keyra and your data safe |
| [Security](docs/security.md) · [Threat model](docs/threat-model.md) · [Vault format](docs/vault-format.md) · [Privacy](docs/privacy.md) | How it is protected |
| [Release notes](docs/release-notes.md) | What changed |

## Help and security

- Questions: see the [FAQ](docs/faq.md). Bugs and ideas: open an [issue](https://github.com/biswajitpanday/keyra-releases/issues/new/choose). Never post real passwords, a real vault, shares or screenshots of real data.
- Vulnerabilities: report them privately, see [SECURITY.md](SECURITY.md).

## License

Free for personal use and for non-profit organizations under the [PolyForm Noncommercial License 1.0.0](LICENSE) © 2026 Biswajit Panday. Use in or for a business needs a paid license: email biswajitmailid@gmail.com. The app has no license key, check or expiry.

This repository holds the installer, the user docs and the security notes; the source code is not published. Third-party licenses: [THIRD-PARTY-NOTICES.txt](THIRD-PARTY-NOTICES.txt).
