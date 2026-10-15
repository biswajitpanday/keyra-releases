# Security policy

Keyra stores passwords, so security reports are welcome and taken seriously.

## Reporting a vulnerability

Please **do not** open a public issue for a security problem.

Report it privately: open this repository's **Security** tab and choose **Report a vulnerability**. Include:

- what you found and why it matters
- steps to reproduce, with a test vault and fake data only (never send real passwords, a real vault or real legacy shares)
- the Keyra version (shown on the lock screen and at the bottom of Settings) and your Windows version

You will get a reply when the report has been read. Please give time for a fix before you publish details.

## Release signing certificate

Releases are signed with a self-signed certificate: subject `CN=Keyra Dev`, SHA-1 thumbprint `AA4E8EA05917E2598282E8120D44F17A1178D97D`, valid until 2031-10-06. Signatures are timestamped. Only the public `.cer` is published. A release signed with any other thumbprint is not from this project; please report it.

## Supported versions

Only the latest release receives security fixes.

## Scope

In scope: the vault file format and its encryption, key derivation, saving and restoring, locking, clipboard handling, CSV import, the Windows Hello gate, legacy shares and snapshots, screen-capture blocking, the tray icon and the second-start signal, the installer package, and anything that could expose account data.

Out of scope, because Keyra cannot protect against them (see [the threat model](docs/threat-model.md)):

- malware or another person with full control of your unlocked Windows session
- a weak or reused master password
- a camera, shoulder surfing, or capture software that gets around Windows display affinity while a value is shown

## Design

How Keyra protects data: [docs/security.md](docs/security.md) and [docs/vault-format.md](docs/vault-format.md).
