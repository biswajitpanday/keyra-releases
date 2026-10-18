# Legacy access

Legacy access lets the people you trust open a read-only copy of chosen items (logins, cards, bank accounts, secure notes, identities) after you are gone,
without your master password.

## How it works

- Keyra makes a random **legacy key** and keeps it only inside your encrypted vault.
- It makes **three shares**. Any **two** of them open the snapshot. One share alone opens nothing.
- The **snapshot** is a separate file holding only the items you chose, encrypted with the legacy key.
- You update the snapshot by hand. Keyra tells you when it is out of date (more than 30 days old and something changed).
- The legacy view never uses Windows Hello unlock; it opens only with two shares.

Give the shares to different people or places (for example: one to a family member, one to a lawyer,
one in a safe). Keep the snapshot file where your heirs can find it (USB stick, cloud folder).
Your heirs need Keyra, the snapshot file and two shares.

What it protects against: a single share or the snapshot file alone reveals nothing.
What it does not protect against: two share holders who work together can open the snapshot.
Anyone with the vault unlocked and the master password can make new shares.

## Crypto

Only built-in .NET primitives:

```
shares s1, s2, s3     = 32 random bytes each
legacy key K          = 32 random bytes (stored only in the vault payload)
set id                = random 24-bit number
pair key Pxy          = HKDF-SHA256(ikm = sx ‖ sy, salt = set id (u32 LE), info = "Keyra legacy v1 pair xy", length 32)
wrap xy               = AES-256-GCM(Pxy, random nonce, K, aad = magic ‖ wrap version (1) ‖ set id ‖ pair id)
snapshot payload      = AES-256-GCM(K, random nonce, UTF-8 JSON, aad = whole snapshot header)
```

The three wraps are made once, when the shares are made, and stored in the vault. "Update snapshot"
copies them into the new file and encrypts only the payload again with a new nonce, so the same shares keep working.
"Make new shares" replaces K, the set id, the shares and the wraps.

## Share text

`KEYRA-XXXX-XXXX-…` with 16 groups of 4 Crockford Base32 characters (64 characters = 40 bytes):

| Bytes | Field |
|---|---|
| 1 | high 4 bits: share format version (`1`); low 4 bits: share number (1–3) |
| 3 | set id (u24 LE) |
| 32 | share |
| 4 | CRC-32 (`System.IO.Hashing.Crc32`) of the 36 bytes before it, LE |

Reading ignores case, spaces and dashes; `O` is read as `0`, `I` and `L` as `1`. The CRC only catches typos; it is not a security check.

## Snapshot file (version 2)

Suggested name `Keyra legacy.keyra-legacy`. All integers little-endian.

| Offset | Size | Field | Value |
|---|---|---|---|
| 0 | 5 | Magic | ASCII `KEYLG` |
| 5 | 2 | Format version | `2` (u16) is written; `1` (Keyra 1.5) is also read |
| 7 | 4 | Set id | u32, only the low 24 bits used |
| 11 | 8 | Snapshot time | i64, Unix seconds UTC |
| 19 | 3 × 61 | Wraps | for pair ids `0x12`, `0x13`, `0x23` (bytes) in this order: pair id (1), nonce (12), wrapped key (32), tag (16) |
| 202 | 1 | AEAD id | `1` = AES-256-GCM |
| 203 | 12 | Nonce | random, new on every snapshot write |
| 215 | n | Ciphertext | payload |
| 215+n | 16 | Tag | GCM tag |

Header = bytes 0–214 and is the payload's associated data. Minimum length 231 bytes.

Payload JSON:

```json
{
  "accounts": [ { "same item fields as in the vault (vault-format.md)": "…" } ],
  "categories": [ { "id": "guid", "name": "Work" } ]
}
```

Only the categories used by the included items. No settings, no legacy info. Version 2 may hold every item kind (cards, bank accounts, secure notes, identities); in version 1 any kind other than Login is refused (**cannot open**).

The wrap version in the associated data and the HKDF info stay version 1, so shares made by Keyra 1.5 open snapshots written by 1.6. Keyra 1.5 refuses a version 2 snapshot ("not a Keyra legacy snapshot, or it is from a newer Keyra"), so an heir with an old Keyra never sees cards as empty logins. Printed shares say to install Keyra 1.6 or newer.

Reading rules (in this order): wrong magic, unknown version, unknown AEAD id, wrong pair ids or length < 231 → **not a snapshot / newer Keyra**;
set id of the shares ≠ file → **wrong set**; wrap tag fails → **cannot open**; payload tag fails → **cannot open**; JSON invalid → **cannot open**.
On any error nothing is shown and nothing is written.

## In the vault

The vault's `legacy` field ([vault-format.md](vault-format.md)) holds: set id, creation time, legacy key, the three wraps,
the snapshot path, the snapshot time and the ids of the items in the last snapshot (for the out-of-date hint).
Each item has `inLegacy` (default false).
