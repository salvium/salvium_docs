# Meta Generator

The **Salvium Meta Generator** is a web application that builds correctly structured, hex-encoded metadata for Salvium token creation. It removes the need to hand-craft JSON and compute hashes manually.

**Live tool:** [meta-generator.salvium.io](https://meta-generator.salvium.io) *(or run locally — see below)*

---

## What it produces

For every token you configure, the Meta Generator outputs:

| Output | Used for |
|--------|----------|
| **Pretty JSON** | Human-readable preview and documentation |
| **Compact JSON** | The exact string that gets hashed and encoded |
| **Hex payload** | The `token_metadata_hex` value passed to `create_token` |
| **SHA-256 hash** | The `hash` value passed to `create_token` |
| **Byte size** | The `size` value passed to `create_token` |

---

## Supported token standards

Select your standard from the form and the relevant fields appear automatically:

| Standard | Fields shown |
|----------|-------------|
| `ERC-721` / `ERC-1155` | Image URI, animation URL, external URL, attributes |
| `ERC-3643` / `ERC-1400` | RWA fields — asset type, jurisdiction, valuation, documents |
| `CIP-108` / `OpenZeppelin-Governor` | Governance fields — proposal text, voting period, quorum |
| Any | Optional compliance section — MiCAR class, LEI, whitepaper |

---

## Using the tool

### 1. Fill in the required fields

- **Name** — the human-readable token name (also written to Tier 1 on-chain)
- **Asset ID** — a unique identifier: ISIN, legal reference, invoice number, or proposal number

### 2. Choose a standard

Select the standard that matches your token type. This controls which extension fields appear. See [Token Types](token-types.md) for guidance.

### 3. Fill in extension fields

For RWA tokens, enter the valuation, jurisdiction, and any document hashes. For NFTs, add the image URI and attributes.

### 4. Review the JSON preview

The right-hand panel updates in real time. Check that the output looks correct before exporting.

### 5. Export

- **Copy hex** — copies the `token_metadata_hex` value ready to paste into a `create_token` call
- **Copy hash** — copies the SHA-256 hash
- **Download `.hex`** — saves the hex payload to a file for CLI workflows

---

## Technical details

The Meta Generator performs these steps in the browser:

1. Builds the Tier 2 JSON object from your form inputs
2. Serialises it to **compact UTF-8 JSON** (no whitespace) — this is the canonical form that gets hashed
3. Encodes the compact JSON to hex (each byte → two hex characters, no `0x` prefix)
4. Computes SHA-256 of the compact JSON bytes
5. Reports the byte length (using `TextEncoder` for accurate UTF-8 byte counting)

!!! info "Why compact JSON?"
    The hash and byte size must match exactly between what the Meta Generator reports and what you send to `create_token`. Any difference in whitespace or key ordering would break the integrity check. Always use the compact form for transport.

---

## Running locally

```bash
git clone https://github.com/salvium/salvium-metadata-generator
cd salvium-metadata-generator
npm install
npm run dev
```

Opens at `http://localhost:5173`.

---

## Integration with create_token

Once you have the three values from the Meta Generator, pass them directly to `create_token`:

```bash
curl http://127.0.0.1:29088/json_rpc \
  -H 'Content-Type: application/json' \
  -d '{
    "jsonrpc": "2.0",
    "id": "0",
    "method": "create_token",
    "params": {
      "ticker": "PROP",
      "supply": 1,
      "name": "123 High Street Property Token",
      "url": "https://your-server.com/metadata/PROP.json",
      "token_metadata_hex": "<hex from Meta Generator>",
      "hash": "<hash from Meta Generator>",
      "size": <size from Meta Generator>
    }
  }'
```

Or pass the metadata object to the [SalviumAssetSDK](sdk.md) and it handles encoding automatically.

---

## Known issues

| Issue | Status |
|-------|--------|
| `token_metadata_hex` previously exported with `0x` prefix — would fail at RPC | **Fixed** in current version |
| CLI command preview previously showed `create_asset` instead of `create_token` | **Fixed** in current version |
