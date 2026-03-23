# Metadata Schema Reference

This page is the complete field reference for Salvium token metadata — the Tier 2 JSON blob that gets hex-encoded and passed to `create_token` as `token_metadata_hex`.

For background on the three-tier architecture, see [Token Protocol](token-protocol.md).

---

## Root object

Every metadata blob is a JSON object. All fields are UTF-8 encoded. Key ordering is not significant, but the canonical form for hashing is **compact JSON** (no whitespace between tokens).

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Human-readable token name. If the optional `name` parameter is also passed to `create_token`, the values should match. |
| `description` | string | No | Plain-text description of the token. |
| `id` | string | No | Unique identifier: ISIN, legal reference, invoice number, proposal ID, or any stable external ID. |
| `technical` | object | Yes | Encoding and standard declaration. See [Technical object](#technical-object). |
| `created_at` | string | Yes | ISO 8601 timestamp of token creation, e.g. `"2026-03-12T00:00:00Z"`. |
| `schema_version` | string | Yes | Metadata schema version. Current value: `"2.0.0"`. |
| `rwa` | object | No | Real-world asset extension. See [RWA extension](#rwa-extension). |
| `nft` | object | No | NFT extension. See [NFT extension](#nft-extension). |
| `invoice` | object | No | Invoice / receivable extension. See [Invoice extension](#invoice-extension). |
| `governance` | object | No | Governance proposal extension. See [Governance extension](#governance-extension). |
| `compliance` | object | No | Regulatory compliance section. See [Compliance section](#compliance-section). |

---

## Technical object

The `technical` object declares which external standard the token follows and how the metadata is encoded.

```json
"technical": {
  "standard": "ERC-3643",
  "encoding": { "charset": "UTF-8" }
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `standard` | string | Yes | Standard identifier. See [Token Types](token-types.md) for valid values. |
| `encoding.charset` | string | Yes | Always `"UTF-8"`. |

### Valid standard values

| Value | Token type |
|-------|-----------|
| `ERC-721` | Non-fungible token (unique, 1-of-1) |
| `ERC-1155` | Multi-edition NFT |
| `ERC-3643` | Security token / real-world asset |
| `ERC-1400` | Partitioned security token / invoice |
| `CIP-108` | On-chain governance proposal |
| `OpenZeppelin-Governor` | OpenZeppelin-compatible governance |

---

## RWA extension

Used for real-world assets: property, equity, commodities, funds. Declare `"standard": "ERC-3643"` or `"ERC-1400"` in `technical`.

```json
"rwa": {
  "asset_type": "real_estate",
  "jurisdiction": "GB",
  "valuation": {
    "amount": 250000,
    "currency": "GBP",
    "date": "2026-01-15"
  },
  "documents": [
    {
      "type": "title_deed",
      "hash": "sha256:a3f1...",
      "url": "https://your-server.com/docs/title.pdf"
    }
  ]
}
```

### `rwa` fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `asset_type` | string | Yes | Asset class. See [Asset type values](#asset-type-values). |
| `jurisdiction` | string | Yes | ISO 3166-1 alpha-2 country code, e.g. `"GB"`, `"US"`, `"DE"`. |
| `valuation` | object | No | Current valuation. See [Valuation object](#valuation-object). |
| `documents` | array | No | Supporting legal documents. See [Document object](#document-object). |

### Asset type values

| Value | Meaning |
|-------|---------|
| `real_estate` | Land or building |
| `equity` | Company shares |
| `debt` | Bond or loan |
| `fund` | Investment fund unit |
| `commodity` | Physical commodity (gold, oil, etc.) |
| `invoice` | Trade receivable (use `invoice` extension instead) |
| `other` | Any other asset class |

### Valuation object

| Field | Type | Description |
|-------|------|-------------|
| `amount` | number | Valuation amount (numeric, no currency symbol). |
| `currency` | string | ISO 4217 currency code, e.g. `"GBP"`, `"USD"`, `"EUR"`. |
| `date` | string | Valuation date in `YYYY-MM-DD` format. |

### Document object

| Field | Type | Description |
|-------|------|-------------|
| `type` | string | Document type: `"title_deed"`, `"prospectus"`, `"audit_report"`, `"legal_opinion"`, etc. |
| `hash` | string | Integrity hash, prefixed with algorithm: `"sha256:<hex>"`. |
| `url` | string | URL to retrieve the document. |

---

## NFT extension

Used for digital collectibles, art, media. Declare `"standard": "ERC-721"` or `"ERC-1155"` in `technical`.

```json
"nft": {
  "image": "https://your-server.com/nft/artwork.png",
  "animation_url": "https://your-server.com/nft/artwork.mp4",
  "external_url": "https://your-server.com/nft/1",
  "edition": 1,
  "edition_max": 100,
  "attributes": [
    { "trait_type": "Artist", "value": "Alice" },
    { "trait_type": "Year", "value": "2026" },
    { "trait_type": "Rarity", "value": "Legendary" }
  ]
}
```

### `nft` fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `image` | string | Yes (for ERC-721) | URI to the primary image. Supports HTTPS, IPFS (`ipfs://`), or Arweave (`ar://`). |
| `animation_url` | string | No | URI to audio/video version of the asset. |
| `external_url` | string | No | Link to the asset's page on an external website. |
| `edition` | number | No | Edition number for multi-edition tokens (ERC-1155). |
| `edition_max` | number | No | Total number of editions in this series. |
| `attributes` | array | No | Trait array. Each entry has `trait_type` (string) and `value` (string or number). |

### Attribute object

| Field | Type | Description |
|-------|------|-------------|
| `trait_type` | string | Name of the trait, e.g. `"Rarity"`, `"Background"`. |
| `value` | string \| number | Trait value. Use numbers for numeric comparisons (e.g. power level), strings for categories. |
| `display_type` | string | Optional. `"number"`, `"boost_percentage"`, `"boost_number"`, `"date"` for marketplace display. |

---

## Invoice extension

Used for trade finance, accounts receivable, factoring tokens. Declare `"standard": "ERC-1400"` in `technical`.

```json
"invoice": {
  "invoice_number": "INV-2026-0042",
  "issuer": "Acme Ltd",
  "debtor": "Buyer Corp",
  "amount": 50000,
  "currency": "GBP",
  "issue_date": "2026-03-01",
  "due_date": "2026-04-01",
  "status": "outstanding"
}
```

### `invoice` fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `invoice_number` | string | Yes | Unique invoice reference number. |
| `issuer` | string | Yes | Legal name of the invoice issuer. |
| `debtor` | string | Yes | Legal name of the party that owes the payment. |
| `amount` | number | Yes | Invoice amount (numeric). |
| `currency` | string | Yes | ISO 4217 currency code. |
| `issue_date` | string | Yes | Invoice issue date in `YYYY-MM-DD`. |
| `due_date` | string | Yes | Payment due date in `YYYY-MM-DD`. |
| `status` | string | No | Current status: `"outstanding"`, `"paid"`, `"overdue"`, `"disputed"`. |

---

## Governance extension

Used for on-chain governance proposals. Declare `"standard": "CIP-108"` or `"OpenZeppelin-Governor"` in `technical`.

```json
"governance": {
  "proposal_id": "CIP-2026-007",
  "proposer": "Community DAO",
  "proposal_text": "Increase block reward by 10% for the next 6 months to support network growth.",
  "voting_start": "2026-03-15T00:00:00Z",
  "voting_end": "2026-03-22T00:00:00Z",
  "voting_period_blocks": 10080,
  "quorum_percentage": 10,
  "options": ["yes", "no", "abstain"]
}
```

### `governance` fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `proposal_id` | string | Yes | Unique proposal identifier. |
| `proposer` | string | Yes | Name or address of the proposing entity. |
| `proposal_text` | string | Yes | Full text of the proposal. May be long — consider hosting at `url` and linking. |
| `voting_start` | string | No | ISO 8601 timestamp when voting opens. |
| `voting_end` | string | No | ISO 8601 timestamp when voting closes. |
| `voting_period_blocks` | number | No | Duration of the voting window in blocks. |
| `quorum_percentage` | number | No | Minimum participation required for a valid vote (0–100). |
| `options` | array | No | Voting options array. Defaults to `["yes", "no", "abstain"]` if omitted. |

---

## Compliance section

Optional. Applies to any token type that must declare regulatory information. Add alongside `rwa`, `nft`, or other extensions as needed.

```json
"compliance": {
  "micar_class": "ART",
  "lei": "213800EXAMPLE000001",
  "whitepaper_url": "https://your-server.com/whitepaper.pdf",
  "whitepaper_hash": "sha256:b2f0..."
}
```

### `compliance` fields

| Field | Type | Description |
|-------|------|-------------|
| `micar_class` | string | EU MiCAR token classification: `"EMT"` (e-money), `"ART"` (asset-referenced), or `"Other"`. |
| `lei` | string | Legal Entity Identifier (20-character ISO 17442 code) of the issuer. |
| `whitepaper_url` | string | URL to the token's whitepaper or prospectus. |
| `whitepaper_hash` | string | Integrity hash of the whitepaper: `"sha256:<hex>"`. |

---

## Encoding rules

These rules apply every time metadata is encoded for `create_token`:

1. **Compact JSON** — no whitespace between tokens. `{"name":"X"}` not `{ "name": "X" }`.
2. **UTF-8** — all string values must be valid UTF-8. The byte size is counted using UTF-8 encoding.
3. **No `0x` prefix** — the hex payload passed to `token_metadata_hex` must not have a `0x` prefix.
4. **SHA-256 of compact bytes** — the `hash` field is a SHA-256 digest of the compact UTF-8 JSON bytes.

The [SalviumAssetSDK](sdk.md) and [Meta Generator](meta-generator.md) handle all of this automatically.

---

## Complete example

A property token with all common sections:

```json
{
  "name": "123 High Street Property Token",
  "description": "Fractional ownership token for UK residential property.",
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
    },
    "documents": [
      {
        "type": "title_deed",
        "hash": "sha256:a3f1c9...",
        "url": "https://your-server.com/docs/title.pdf"
      }
    ]
  },
  "compliance": {
    "micar_class": "ART",
    "lei": "213800EXAMPLE000001",
    "whitepaper_url": "https://your-server.com/whitepaper.pdf",
    "whitepaper_hash": "sha256:b2f0..."
  }
}
```

