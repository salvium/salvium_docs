# Token Types

Salvium supports four token categories, each with its own metadata structure. The category is declared in the `technical.standard` field of the [Tier 2 metadata](token-protocol.md).

---

## NFT / Collectible

**Standards:** `ERC-721`, `ERC-1155`

Use for unique digital items — art, certificates, collectibles, in-game assets.

```json
{
  "name": "Salvium Genesis NFT",
  "description": "The first NFT minted on the Salvium chain.",
  "id": "SAL-NFT-0001",
  "technical": {
    "standard": "ERC-721",
    "encoding": { "charset": "UTF-8" }
  },
  "created_at": "2026-03-12T00:00:00Z",
  "schema_version": "2.0.0",
  "nft": {
    "image": "ipfs://QmXxx...",
    "external_url": "https://salvium.io/nft/genesis",
    "attributes": [
      { "trait_type": "Edition", "value": "Genesis" },
      { "trait_type": "Year",    "value": 2026, "display_type": "number" }
    ]
  }
}
```

| Field | Description |
|-------|-------------|
| `nft.image` | URI to the primary image (IPFS or HTTPS) |
| `nft.animation_url` | URI to video, audio, or 3D content |
| `nft.external_url` | Link to more information |
| `nft.attributes[]` | Array of traits — compatible with OpenSea and NFT marketplaces |

---

## Real-World Asset (RWA)

**Standards:** `ERC-3643`, `ERC-1400`

Use for tokenised physical assets — property, bonds, commodities, invoices used as collateral.

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
        "hash": "0xabc123...",
        "url": "ipfs://QmYyy..."
      }
    ],
    "claim_topics": [1, 7]
  }
}
```

| `rwa` field | Description |
|-------------|-------------|
| `asset_type` | `real_estate`, `bond`, `commodity`, `invoice`, etc. |
| `jurisdiction` | ISO 3166-1 alpha-2 country code |
| `valuation.amount` | Asset value |
| `valuation.currency` | ISO 4217 currency code |
| `documents[]` | Legal documents — each has a hash for integrity verification |
| `claim_topics` | ERC-3643 compliance claim topic IDs |

---

## Invoice / Security Token

**Standard:** `ERC-1400`

Use for regulated securities or invoice finance. Supply is typically 1 (the invoice itself) or fractional (shares).

```json
{
  "name": "Invoice #2026-0042 — Acme Ltd",
  "description": "Unpaid invoice used as collateral for short-term finance.",
  "id": "INV-2026-0042",
  "technical": {
    "standard": "ERC-1400",
    "encoding": { "charset": "UTF-8" }
  },
  "created_at": "2026-03-12T00:00:00Z",
  "schema_version": "2.0.0",
  "rwa": {
    "asset_type": "invoice",
    "jurisdiction": "GB",
    "valuation": {
      "amount": 12500,
      "currency": "GBP",
      "date": "2026-03-12"
    }
  }
}
```

!!! tip
    ERC-1400 also uses the `rwa{}` extension — the distinction from ERC-3643 is primarily about the transfer restriction model, which is handled at the application layer.

---

## Governance Proposal

**Standards:** `CIP-108`, `OpenZeppelin-Governor`

Use for DAO governance — proposals, votes, treasury allocations.

```json
{
  "name": "Treasury Allocation — Q2 2026",
  "description": "Proposal to allocate 50,000 SAL from treasury to ecosystem grants.",
  "id": "PROP-2026-007",
  "technical": {
    "standard": "CIP-108",
    "encoding": { "charset": "UTF-8" }
  },
  "created_at": "2026-03-12T00:00:00Z",
  "schema_version": "2.0.0",
  "governance": {
    "proposal_text": "This proposal allocates 50,000 SAL to the ecosystem grants fund for Q2 2026.",
    "voting_period_days": 7,
    "quorum_threshold": 0.1,
    "execution": {
      "target": "treasury",
      "amount": 50000,
      "currency": "SAL"
    }
  }
}
```

| `governance` field | Description |
|-------------------|-------------|
| `proposal_text` | Full text of the proposal |
| `voting_period_days` | How long voting is open |
| `quorum_threshold` | Minimum participation (0.0–1.0) |
| `execution` | What happens if the proposal passes |

---

## Compliance extension

Any token type can include a `compliance{}` object for EU MiCAR-regulated assets:

```json
{
  "compliance": {
    "micar_class": "ART",
    "lei": "213800EXAMPLE00001",
    "whitepaper_hash": "0xdef456...",
    "whitepaper_url": "https://example.com/whitepaper.pdf"
  }
}
```

| Field | Description |
|-------|-------------|
| `micar_class` | `ART` (Asset-Referenced Token), `EMT` (E-Money Token), or `other` |
| `lei` | Legal Entity Identifier (20-char ISO 17442) |
| `whitepaper_hash` | SHA-256 of the regulatory whitepaper |
| `whitepaper_url` | Location of the whitepaper |

---

## Choosing the right type

```
Is it a unique digital item?                    → ERC-721
Is it a physical asset or financial instrument? → ERC-3643 or ERC-1400
Is it a DAO vote or proposal?                   → CIP-108
Is it EU-regulated?                             → Add compliance{} to any type
Everything else?                                → ERC-721 or leave standard as custom
```
