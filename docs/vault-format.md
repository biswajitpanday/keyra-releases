# Vault format (version 5)

File: `vault.keyra`. One binary file. All integers are little-endian.

## Layout

| Offset | Size | Field | Value |
|---|---|---|---|
| 0 | 5 | Magic | ASCII `KEYRA` |
| 5 | 2 | Format version | `5` (u16) is written; `4` (Keyra 1.5) is also read |
| 7 | 1 | KDF id | `1` = Argon2id |
| 8 | 4 | KDF memory (KiB) | u32, allowed 8 192 – 1 048 576 (default 262 144 = 256 MiB) |
| 12 | 4 | KDF iterations | u32, allowed 1 – 10 (default 8) |
| 16 | 1 | KDF parallelism | must be `1` (libsodium/NSec limit) |
| 17 | 16 | Salt | random, new on create and on master password change |
| 33 | 1 | AEAD id | `1` = AES-256-GCM |
| 34 | 12 | Nonce | random, new on every save |
| 46 | n | Ciphertext | AES-256-GCM of the UTF-8 JSON payload |
| 46+n | 16 | Tag | GCM authentication tag |

Header = bytes 0–45 (46 bytes). It is plaintext and holds no secrets.
The whole header is the GCM **associated data**, so changing any header byte makes authentication fail.

Minimum valid length: 62 bytes (header + tag). A file larger than 64 MB is not read.

## Key

`key = Argon2id(password = UTF-8(NFC(master password)), salt, memory, iterations, parallelism = 1, length = 32)`

## Payload (inside the ciphertext)

```json
{
  "accounts": [
    { "id": "guid", "kind": "Login", "website": "…", "email": "…", "password": "…",
      "createdAt": "ISO-8601", "updatedAt": "ISO-8601",
      "url": "https://… or null", "notes": "… or null", "passwordChangedAt": "ISO-8601 or null",
      "categoryId": "guid or null", "isFavorite": false, "isProtected": false, "inLegacy": false,
      "recoveryEmail": "… or null", "recoveryPhone": "… or null", "pin": "… or null",
      "securityQuestions": [ { "question": "…", "answer": "…" } ],
      "passwordHistory": [ { "password": "…", "usedFrom": "ISO-8601", "replacedAt": "ISO-8601" } ],
      "deletedAt": "ISO-8601 or null",
      "card": null or { "cardholder": "…", "number": "…", "brand": "Visa", "validFrom": { "month": 1, "year": 2025 },
                        "expiry": { "month": 8, "year": 2028 }, "cvv": "…", "billingAddress": "…", "bankPhone": "…" },
      "bank": null or { "bankName": "…", "accountHolder": "…", "accountNumber": "…", "accountType": "Current",
                        "iban": "…", "bic": "…", "routingCode": "…", "branch": "…", "branchAddress": "…", "bankPhone": "…" },
      "identity": null or { "title": "Mx", "fullName": "…", "company": "…", "birthDate": "yyyy-MM-dd", "email": "…",
                            "phone": "…", "address": "…", "username": "…", "passportNumber": "…", "passportExpiry": "yyyy-MM-dd",
                            "nationalId": "…", "nationalIdExpiry": "yyyy-MM-dd", "licenceNumber": "…",
                            "licenceExpiry": "yyyy-MM-dd", "taxNumber": "…" } }
  ],
  "categories": [ { "id": "guid", "name": "Personal" } ],
  "legacy": null or { "setId": 1234567, "createdAt": "ISO-8601", "key": "base64 (32 bytes)",
              "wraps": [ "base64 (61 bytes)", "…", "…" ], "fileName": "path or null",
              "snapshotAt": "ISO-8601 or null", "snapshotIds": [ "guid" ] },
  "settings": { "autoLockMinutes": 5, "clipboardSeconds": 30, "lockOnMinimize": true, "lockOnSessionLock": true,
                "excludeFromCapture": true, "blurRows": false, "minimizeToTray": false, "tourSeen": false,
                "lastBackupAt": "ISO-8601 or null" },
  "changedAt": "ISO-8601 or null"
}
```

- Each entry of `accounts` is an item. `kind` is `Login`, `Card`, `BankAccount`, `SecureNote` or `Identity`; a missing `kind` means `Login`. `website` is the name of the item. A Card has `card`, a BankAccount `bank` and an Identity `identity`; every other kind has none of them. Items other than Logins have an empty `email` and `password` and no `url`, `passwordChangedAt`, recovery fields, security questions or password history. The `pin` of a Card or BankAccount is the card or bank PIN; Secure notes and Identities have no `pin`. A Secure note keeps its text in `notes`. Brand, account type and title are names from fixed lists (a number is accepted only if it is a defined value); months are 1–12 and years 2000–2099.
- `accounts`, `categories` and `settings` are required. A missing field inside `settings` uses its default; `autoLockMinutes = 0` means never. An unknown auto-lock or clipboard value falls back to the default.
- `deletedAt` marks an item in Trash. Items in Trash for 30 days or more are removed when the vault is saved.
- `lastBackupAt` is the time of the last successful backup and `changedAt` the time of the last save of items or categories; together they drive the backup reminder.
- A `categoryId` that matches no category is read as none. `passwordHistory` loses entries equal to the current password, then is sorted newest first and cut to 5 entries.
- `legacy` holds the legacy-access key and wraps (see [legacy-access.md](legacy-access.md)). It is null until legacy access is set up; a present `legacy` must have a 32-byte key and exactly three 61-byte wraps, else **format error**.
- Field length limits (website, email, PIN, recovery email/phone, questions, categories) are checked when values are typed or imported, not when a file is read.
- Unknown JSON properties are ignored.

## Reading rules (in this order)

1. Version higher than 5 → **newer version** ("saved by a newer version of Keyra"). Version 1 to 3 → **early version** ("made by an early test version of Keyra"). Length < 62, wrong magic, version 0, unknown KDF id or unknown AEAD id → **format error**.
2. KDF parameters outside the allowed ranges → **format error**. This is checked before running Argon2id, so a modified file cannot force huge memory or time use.
3. Derive key, decrypt. Tag mismatch → **authentication error** (wrong password or modified file; the two cannot be told apart).
4. JSON invalid, missing required parts, duplicate account or category ids, or empty (null) list items → **format error**. Also a format error: an unknown `kind`, brand, account type or title (also an undefined number); details that do not match the kind; an item other than a Login with login-only fields; a month outside 1–12 or a year outside 2000–2099; and in version 4 any kind other than Login.

On any error: no data is shown, the file is not changed, and no new vault is created.

## Writing

1. Keyra remembers the SHA-256 of the bytes it read at unlock or last wrote.
2. New random nonce → encrypt → write `vault.keyra.<guid>.tmp` in the same folder (write-through, flush to disk).
3. Right before the move, read the vault again. If its bytes are not the remembered ones (changed by another PC, OneDrive or another program, or missing), nothing is written and the vault locks.
4. `File.Move(overwrite: true)`. A file that another program holds open shows up as access denied or a sharing violation; those are retried for about 4.5 s. A read-only vault file fails at once. Creating a new vault uses `overwrite: false`.

The first write of a vault that was opened as version 4 first copies the file as it is to `vault.keyra.pre-1.6.bak` (or `vault.keyra.pre-1.6-<yyyyMMdd-HHmmss>.bak` if that name exists, with a random suffix if that exists too) in the same folder: only bytes that match the remembered SHA-256, written atomically like a save, never replacing a file. If the copy fails, nothing is written. Keyra 1.5 opens that copy after it is renamed to `vault.keyra` (see the FAQ); it refuses version 5 with its "newer version" message and never writes it.

After an unlock, weaker-than-default KDF parameters are raised (per field, the larger of file and default) with a new salt, in one extra save. Stale `vault.keyra.*.tmp` files are deleted at startup.

## Versions

Version 5 is the format of Keyra 1.6 (item kinds). Version 4 is the format of Keyra 1.5: 1.6 reads it (every item is a Login) and writes version 5 on the first save, after the copy above. Versions 1 to 3 were used only by early test versions and are not read. A future format gets version `6`. Unknown versions are always rejected, never guessed.

## Example (header of a test vault)

Salt, nonce and ciphertext are random in every file.

```
0000  4B455952410500010000040008000000  KEYRA...........   magic, version 5, Argon2id, 256 MiB, t=8
0010  017A7E5A606A5E3B73003E0494078940  .z~Z`j^;s.>....@   p=1, salt…
0020  5201ADAA3A637263B267B9FBE8EBE8D3  R...:crc.g......   …salt, AEAD=1, nonce…
0030  549A0A079339753EBE59BFD39E72FF35  T....9u>.Y...r.5   ciphertext…
```
