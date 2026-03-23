# Token Protocol

Salvium's token system uses a **three-tier architecture** that separates what must be on-chain from what can live off-chain. This keeps transaction sizes small while allowing rich, structured metadata.

---

## The three tiers

```
┌─────────────────────────────────────────────────────────┐
│  TIER 1 — On-chain (inside every token transaction)     │
│  _name · _uri · _documentHash · _documentSize           │
└───────────────────┬─────────────────────────────────────┘
                    │ _uri points to
┌───────────────────▼─────────────────────────────────────┐
│  TIER 2 — Off-chain JSON blob (at the _uri address)     │
│  name · description · id · technical · created_at       │
│  schema_version                                          │
└───────────────────┬─────────────────────────────────────┘
                    │ embedded inside Tier 2
┌───────────────────▼─────────────────────────────────────┐
│  TIER 3 — Extension objects (inside the Tier 2 blob)    │
│  rwa{} · governance{} · compliance{} · attributes[]     │
└─────────────────────────────────────────────────────────┘
```

---

## Tier 1 — On-chain fields

These four fields are written into the Salvium transaction and validated by the protocol.

| Field | Max size | Description |
|-------|----------|-------------|
| `_name` | 160 bytes | Human-readable token name |
| `_uri` | 768 bytes | IPFS CID or HTTPS URL pointing to the Tier 2 JSON blob |
| `_documentHash` | 64 bytes | SHA-256 hex of the Tier 2 blob — proves the metadata hasn't changed |
| `_documentSize` | 8 bytes | Byte length of the Tier 2 blob |

!!! note
    In wallet-rpc calls these are passed as `name`, `url`, `hash`, and `size` — the `_` prefix is the protocol-level naming convention.

---

## Tier 1 — How it is passed to `create_token`

The Tier 1 data is passed to `create_token` in two ways:

1. **As individual params** — `name`, `url`, `hash`, `size` (alongside `ticker` and `supply`)
2. **As `token_metadata_hex`** — the same four fields serialised as compact JSON and hex-encoded:

```json
{ "name": "123 High Street Property Token", "url": "https://...", "hash": "f0a7f0...", "size": 387 }
```

```
token_metadata_hex = hex( JSON.stringify({ name, url, hash, size }) )
```

The `hash` and `size` values are computed from the **Tier 2** blob (not the Tier 1 JSON).

---

## Tier 2 — The metadata blob

The Tier 2 blob is a JSON document hosted at the Tier 1 `url`. It is **not** passed to `create_token` directly. These six fields are required:

```json
{
  "name": "123 High Street Property Token",
  "description": "Fractional ownership token for UK residential property",
  "id": "GB-PROP-2026-0001",
  "technical": {
    "standard": "ERC-3643",
    "encoding": { "charset": "UTF-8" }
  },
  "created_at": "2026-03-12T00:00:00Z",
  "schema_version": "2.0.0"
}
```

The [Meta Generator](meta-generator.md) builds this blob. Its SHA-256 hash and byte size go into the Tier 1 JSON (and the individual `hash`/`size` params).

---

## Tier 3 — Extension objects

Tier 3 data sits **inside** the Tier 2 blob. The extension object used depends on the token standard:

| Standard | Extension object | Purpose |
|----------|-----------------|---------|
| `ERC-721`, `ERC-1155` | *(no named object)* | `image`, `attributes[]`, `animation_url` added to root |
| `ERC-3643`, `ERC-1400` | `rwa{}` | Asset valuation, jurisdiction, legal documents |
| `CIP-108`, `OpenZeppelin-Governor` | `governance{}` | Proposal text, voting parameters |
| Any regulated token | `compliance{}` | MiCAR classification, LEI, whitepaper hash |

Example with a Tier 3 `rwa` extension:

```json
{
  "name": "123 High Street Property Token",
  "description": "Fractional ownership token for UK residential property",
  "id": "GB-PROP-2026-0001",
  "technical": {
    "standard": "ERC-3643",
    "encoding": { "charset": "UTF-8" }
  },
  "created_at": "2026-03-12T00:00:00Z",
  "schema_version": "2.0.0",
  "rwa": {
    "asset_type": "real_estate",
    "jurisdiction": "GB",
    "valuation": {
      "amount": 250000,
      "currency": "GBP",
      "date": "2026-01-15"
    }
  }
}
```

---

## Why this design?

**Privacy** — Salvium transactions are private by default (RingCT, stealth addresses). Storing large metadata blobs on-chain would increase transaction weight. The three-tier split keeps on-chain data minimal.

**Integrity** — The SHA-256 hash in Tier 1 cryptographically binds the on-chain token to its off-chain metadata. If the document at `_uri` is tampered with, the hash won't match.

**Flexibility** — The Tier 3 extension model means the same on-chain structure supports NFTs, RWAs, governance tokens, and anything else — without protocol changes.

**Familiarity** — Using ERC-721/3643/1400 and CIP-108 as standard labels means tooling from the Ethereum and Cardano ecosystems can recognise and interpret Salvium token metadata, even though Salvium has no EVM.

---

## On-chain flow

```
1. Build the Tier 2 JSON blob (name, description, id, technical{}, + Tier 3 extensions)
2. Serialise to compact UTF-8 JSON (no whitespace)
3. Compute SHA-256 hash of the Tier 2 bytes           → hash
4. Compute byte length of the Tier 2 bytes            → size
5. Host the Tier 2 blob somewhere accessible          → url
6. Build Tier 1 JSON: { "name", "url", "hash", "size" }
7. Hex-encode Tier 1 JSON (no 0x prefix)              → token_metadata_hex
8. Call create_token:
      ticker, supply, name          ← on-chain primitives
      token_metadata_hex            ← hex of Tier 1 JSON
      url, hash, size               ← also passed as individual params
9. wallet-rpc builds and broadcasts the transaction
10. Once confirmed, the token appears in get_tokens
```

See [Getting Started](getting-started.md) for a walkthrough of this flow.
