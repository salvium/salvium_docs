# Salvium RWA/NFT Demo — Technical Notes & Build Record

**Last updated:** 2026-03-13
**Tested against:** wallet-rpc v1.1.0-RC2, Salvium testnet
**Status:** Built and running. All four components shipped.

> **Correction from Neil (2026-03-13):** `token_metadata_hex` is hex-encoded **Tier 1 JSON** — not Tier 2.
> The Tier 1 JSON contains: `{ "name": "...", "url": "...", "hash": "...", "size": N }`.
> The Tier 2 blob is the off-chain JSON hosted at `url`. The SDK currently encodes the full Tier 2
> metadata object into `token_metadata_hex` — this is wrong and must be fixed.
> See Section 12 for full impact and required code changes.

---

## 1. What Was Built

A full token lifecycle stack — four components working together:

```
Browser (React SPA)
    ↓  plain HTTP POST (no auth)
Middleware  :3001   (Express.js)
    ↓  HTTP Digest auth
wallet-rpc  :29088
    ↓
Salvium testnet daemon  :29081 / :29082
```

### Components

| Component | Location | What it does |
|-----------|----------|--------------|
| **SalviumAssetSDK** | `salvium/asset-sdk/src/` | TypeScript library wrapping wallet-rpc. Handles encoding, hashing, Digest auth. |
| **Middleware** | `salvium/middleware/src/server.ts` | Express.js proxy. Solves browser CORS + Digest auth. Exposes REST API. |
| **Demo UI** | `salvium/demo-ui/src/` | React SPA. Marketplace, create, and token detail screens. Demo/live mode. |
| **Meta Generator** | `salvium/meta-generator/src/` | Pre-existing tool. Bug fixes applied (see Section 9). |
| **Knowledge base** | `salvium/docs/docs/TOKENS/` | 10 MkDocs pages covering everything. |

---

## 2. Token Protocol — Confirmed Architecture

### The Chain
- CryptoNote-based (Monero fork) — **not EVM**
- Private by default: RingCT, stealth addresses, ring signatures
- Ethereum/Cardano standards (ERC-721, ERC-3643, etc.) used as **metadata design conventions only** — no smart contracts, no EVM

### Three-Tier Architecture

| Tier | Where stored | Contents | Who validates |
|------|-------------|----------|---------------|
| 1 | On-chain — hex-encoded as `token_metadata_hex` | JSON: `{ "name", "url", "hash", "size" }` | Protocol |
| 2 | Off-chain — JSON blob hosted at Tier 1 `url` | Structured metadata: fixed fields + extension objects | SDK / Meta Generator |
| 3 | Embedded in Tier 2 blob | Domain extensions: `rwa{}`, `nft{}`, `invoice{}`, `governance{}`, `compliance{}` | Application |

**How it fits together:**

```
create_token call
  ├── ticker, supply          ← direct params (primitive on-chain identifiers)
  ├── name                    ← direct param (also in Tier 1 JSON for convenience)
  ├── token_metadata_hex      ← hex( JSON.stringify({ name, url, hash, size }) )
  ├── url                     ← URI to the Tier 2 blob (also inside token_metadata_hex)
  ├── hash                    ← SHA-256 of the Tier 2 blob (also inside token_metadata_hex)
  └── size                    ← byte length of the Tier 2 blob (also inside token_metadata_hex)

Tier 2 blob  (hosted at url, off-chain)
  └── Full metadata JSON: name, description, id, technical{}, created_at, schema_version
        └── Tier 3 extensions: rwa{}, nft{}, invoice{}, governance{}, compliance{}
```

**Correct encoding flow:**
1. Build Tier 2 JSON metadata object
2. Serialise to compact UTF-8 JSON (no whitespace)
3. Compute SHA-256 hash of Tier 2 bytes → `hash`
4. Compute byte length of Tier 2 bytes → `size`
5. Host Tier 2 JSON somewhere → `url`
6. Build Tier 1 JSON: `{ "name": "...", "url": "...", "hash": "...", "size": N }`
7. Hex-encode Tier 1 JSON (no `0x` prefix) → `token_metadata_hex`
8. Call `create_token` with all params

### `create_token` Parameters

| Field | Notes |
|-------|-------|
| `ticker` | Exactly 4 characters. Stored on-chain with `sal` prefix (e.g. `PROP` → `salPROP`). |
| `supply` | Total token supply. |
| `name` | Display name. Also included inside `token_metadata_hex` Tier 1 JSON. |
| `token_metadata_hex` | Hex-encoded **Tier 1 JSON**: `{"name":"...","url":"...","hash":"...","size":N}`. No `0x` prefix. **Accepted but not stored in RC2 — see Section 8.** |
| `url` | URI to the Tier 2 blob. Also encoded inside `token_metadata_hex`. **Not stored in RC2.** |
| `hash` | SHA-256 hex of the **Tier 2** blob. Also encoded inside `token_metadata_hex`. **Not stored in RC2.** |
| `size` | Byte length of the **Tier 2** blob. Also encoded inside `token_metadata_hex`. **Not stored in RC2.** |

### Tier 2 Metadata Structure (every token)

```json
{
  "name": "string",
  "description": "string",
  "id": "string — ISIN, legal ref, invoice number, proposal ID",
  "technical": {
    "standard": "ERC-721 | ERC-3643 | ERC-1400 | CIP-108",
    "encoding": { "charset": "UTF-8" }
  },
  "created_at": "ISO 8601 UTC",
  "schema_version": "2.0.0"
}
```

### Tier 3 Extensions

| Token type | `technical.standard` | Extension key | Key fields |
|------------|---------------------|---------------|------------|
| NFT | `ERC-721`, `ERC-1155` | `nft{}` | `image`, `animation_url`, `attributes[]`, `edition`, `edition_max` |
| Real-World Asset | `ERC-3643` | `rwa{}` | `asset_type`, `jurisdiction`, `valuation{}`, `documents[]` |
| Invoice | `ERC-1400` | `invoice{}` | `invoice_number`, `issuer`, `debtor`, `amount`, `currency`, `due_date`, `status` |
| Governance | `CIP-108` | `governance{}` | `proposal_id`, `proposal_text`, `voting_period_blocks`, `quorum_percentage`, `options[]` |
| Any | any | `compliance{}` | `micar_class`, `lei`, `whitepaper_url`, `whitepaper_hash` |

---

## 3. wallet-rpc — Confirmed Setup

```bash
# Daemon (testnet, offline/private, fixed difficulty for fast mining)
salviumd --testnet --offline --fixed-difficulty 500

# wallet-rpc (do NOT pass --offline here — it prevents daemon sync)
salvium-wallet-rpc \
  --wallet-file C:\salvium\wallet2 \
  --password demo \
  --testnet \
  --daemon-address 127.0.0.1:29081 \
  --rpc-bind-port 29088 \
  --disable-rpc-login
```

**Key points:**
- `--offline` on daemon = runs private testnet with no peers. Correct.
- `--offline` on wallet-rpc = prevents sync. **Do not use.**
- Start wallet-rpc with `--disable-rpc-login` for testnet — no auth needed on curl calls.
- If you do use `--rpc-login`, add `-u <user>:<pass> --digest` to every curl command. The SDK handles Digest auth automatically when `username`/`password` are set in `SDKConfig`.
- Daemon binds **two ports**: `29081` for JSON-RPC, `29082` for HTTP endpoints (`/start_mining`, `/stop_mining`)
- `create_token` requires chain height > 1200

### Mining (testnet setup)

```bash
# Start mining (HTTP endpoint on port 29082 — not json_rpc)
curl http://127.0.0.1:29082/start_mining \
  -d '{"do_background_mining":false,"ignore_battery":true,"miner_address":"<address>","threads_count":1}' \
  -H 'Content-Type: application/json'

# Stop mining
curl http://127.0.0.1:29082/stop_mining -H 'Content-Type: application/json'

# Check height (json_rpc on port 29081)
curl http://127.0.0.1:29081/json_rpc \
  -d '{"jsonrpc":"2.0","id":"0","method":"get_info"}' \
  -H 'Content-Type: application/json'
```

**Warning:** Mining many thousands of blocks to a single wallet creates thousands of tiny UTXOs. `create_token` hangs indefinitely during coin selection when the UTXO set is very large (~674 billion outputs observed). Use a clean wallet with minimal UTXOs for the demo.

---

## 4. wallet-rpc Methods — Confirmed Behaviour

### `create_token`

```bash
curl http://127.0.0.1:29088/json_rpc \
  -d '{
    "jsonrpc": "2.0",
    "id": "0",
    "method": "create_token",
    "params": {
      "ticker": "PROP",
      "supply": 1,
      "name": "123 High Street Property Token"
    }
  }' \
  -H 'Content-Type: application/json'
```

**Response:**
```json
{
  "result": {
    "tx_hash_list": ["2ef96b5f128ad437367ed076775eddfb..."],
    "fee_list": [10521160],
    "amount_list": [0],
    "weight_list": [20012]
  }
}
```

**Confirmed rules:**
- `ticker` must be **exactly 4 characters**. `"TST1"` works, `"TEST1"` returns error `-7: Asset type must be exactly 4 characters long.`
- `token_metadata_hex` must have **no `0x` prefix**. `"7b22..."` works, `"0x7b22..."` returns error `-1: Failed to parse metadata hex`.
- Fee is ~10,521,160 atomic units (~0.01 SAL) per token creation.
- Returns `tx_hash_list` and `fee_list` as arrays (always index `[0]` for single token creation).

**Error codes:**

| Code | Message | Cause |
|------|---------|-------|
| `-7` | `Create_token is not available yet.` | Chain height below 1200 |
| `-7` | `Asset type must be exactly 4 characters long.` | Ticker not exactly 4 chars |
| `-1` | `Failed to parse metadata hex` | `token_metadata_hex` has `0x` prefix or invalid hex |

### `get_tokens`

```bash
curl http://127.0.0.1:29088/json_rpc \
  -d '{"jsonrpc":"2.0","id":"0","method":"get_tokens"}' \
  -H 'Content-Type: application/json'
```

**Response:**
```json
{ "result": { "tokens": ["salPROP", "salNFT1", "salTST1"] } }
```

- Tickers are returned with `sal` prefix (e.g. `PROP` → `salPROP`)
- **RC2 bug:** Returns `{"error":{"code":-1,"message":"Failed to get token data from wallet."}}` when wallet has no tokens instead of `{"tokens":[]}`. SDK normalises this to empty array.

### `get_token_info`

```bash
curl http://127.0.0.1:29088/json_rpc \
  -d '{
    "jsonrpc": "2.0",
    "id": "0",
    "method": "get_token_info",
    "params": { "asset_type": "salPROP" }
  }' \
  -H 'Content-Type: application/json'
```

**Response:**
```json
{
  "result": {
    "asset_type": "PROP",
    "sal_token": {
      "name": "123 High Street Property Token",
      "supply": 1,
      "url": "",
      "size": 0
    },
    "status": "OK",
    "token_type": 2,
    "version": 1
  }
}
```

- `asset_type` param takes `sal`-prefixed form; response strips the prefix
- `token_type: 2` = user-created SAL token (as distinct from native SAL1)
- `url` and `size` always return empty/0 in RC2 — consequence of metadata not being stored (see Section 8)

### Standard wallet methods (also work with token wallets)

| Method | Notes |
|--------|-------|
| `getbalance` | Returns balances for all asset types including SAL1 and custom tokens |
| `get_height` | Returns current wallet sync height |
| `refresh` | Forces wallet sync with daemon |

---

## 5. SalviumAssetSDK — What Was Built

**Location:** `salvium/asset-sdk/src/`

**Files:**
- `types.ts` — TypeScript interfaces: `SDKConfig`, `TokenMetadata`, `CreateTokenParams`, `CreateTokenResult`, `TokenInfo`
- `digestAuth.ts` — HTTP Digest auth implementation using Node's `http`/`https` modules
- `index.ts` — `SalviumAssetSDK` class

**Usage:**
```typescript
// Direct to wallet-rpc (Digest auth)
const sdk = new SalviumAssetSDK({
  rpcUrl: 'http://127.0.0.1:29088/json_rpc',
  username: '1',
  password: '1',
});

// Via middleware (no auth needed)
const sdk = new SalviumAssetSDK({
  rpcUrl: 'http://127.0.0.1:3001',
});

// Create token
const result = await sdk.createToken({
  ticker: 'PROP',   // exactly 4 chars
  supply: 1,
  name: '123 High Street Property Token',
  metadata: { /* TokenMetadata object */ },
});
// result.tx_hash, result.fee, result.hex_payload, result.hash, result.size

// List tokens
const tickers = await sdk.listTokens(); // ['salPROP', 'salNFT1']

// Get token info
const token = await sdk.getToken('PROP'); // or 'salPROP' — prefix optional
```

**What the SDK does automatically:**
1. Validates ticker is exactly 4 characters (throws client-side before RPC call)
2. Serialises `metadata` to compact UTF-8 JSON (no whitespace)
3. Hex-encodes it — **no `0x` prefix**
4. Computes SHA-256 hash of the compact JSON bytes
5. Computes byte size using `Buffer.byteLength`
6. Calls `create_token` with all parameters
7. Normalises the `get_tokens` empty-wallet error to `[]`
8. Accepts ticker with or without `sal` prefix in `getToken()`

### Critical: HTTP Digest Auth

**Problem encountered:** Node.js built-in `fetch` (Node 20) fails to properly retransmit the request body after receiving a 401 challenge during Digest auth. The second request arrives at wallet-rpc without a body, causing it to fail.

**Root cause:** Node 20's `fetch` follows redirects but does not implement the Digest auth retry-with-body cycle correctly.

**Fix:** `digestAuth.ts` uses Node's `http`/`https` modules directly via a custom `request()` helper, which correctly retransmits the full body on both the initial (unauthenticated) request and the authenticated retry.

The implementation:
1. Sends the POST request without auth → receives `401` with `WWW-Authenticate: Digest ...` header
2. Parses the challenge (handles both quoted and unquoted values, uses first challenge only, MD5 not MD5-sess)
3. Computes `HA1 = md5(username:realm:password)`, `HA2 = md5(POST:/json_rpc)`, `response = md5(HA1:nonce:nc:cnonce:qop:HA2)`
4. Resends the full request body with `Authorization: Digest ...` header

---

## 6. Middleware — What Was Built

**Location:** `salvium/middleware/src/server.ts`

Single Express.js file. Uses `SalviumAssetSDK` internally.

**Start:**
```bash
cd salvium/middleware
RPC_URL=http://127.0.0.1:29088/json_rpc \
RPC_USER=1 \
RPC_PASS=1 \
PORT=3001 \
npx tsx src/server.ts
```

**Environment variables:**

| Variable | Default | Description |
|----------|---------|-------------|
| `RPC_URL` | `http://127.0.0.1:29088/json_rpc` | wallet-rpc endpoint |
| `RPC_USER` | *(empty)* | Digest auth username |
| `RPC_PASS` | *(empty)* | Digest auth password |
| `PORT` | `3001` | Middleware listen port |

If `RPC_USER`/`RPC_PASS` are empty, SDK skips Digest auth (suitable for `--disable-rpc-login`).

**Endpoints:**

| Method | Path | Action |
|--------|------|--------|
| `GET` | `/api/assets` | Lists all tokens. Returns `{"tokens":["salPROP",...]}`. Normalises empty-wallet error to `[]`. |
| `GET` | `/api/assets/:ticker` | Returns token info. Accepts with or without `sal` prefix. |
| `POST` | `/api/assets` | Creates a token. Body: `{ticker, supply, name, metadata?, url?}`. |

**CORS:** `Access-Control-Allow-Origin: *` — appropriate for local demo. Restrict for production.

**Why middleware is required:**
- Browsers cannot make HTTP Digest authenticated requests (the `fetch` API has no Digest support)
- wallet-rpc does not set CORS headers — direct browser requests are blocked

---

## 7. Demo UI — What Was Built

**Location:** `salvium/demo-ui/src/`
**Stack:** React 19, TypeScript, Vite 5, Tailwind CSS 3, React Router 7 (HashRouter)

**Key files:**
- `App.tsx` — Root. Runs single `checkConnection()` call, passes `live` state to all pages.
- `api.ts` — API layer with mock fallback (`MOCK_TOKENS`), connection check, `listTokens()`, `getToken()`, `createToken()`
- `components/Layout.tsx` — Header (Salvium wordmark), live/demo status pill, footer with RC2 tooltip
- `components/DemoBanner.tsx` — Two-panel: explains demo mode + complete 3-step startup instructions
- `pages/MarketplacePage.tsx` — Hero section, "How it works" 3-step strip, token grid
- `pages/CreatePage.tsx` — Template selector (4 templates), form with help text, spinner steps, stores tx in `sessionStorage`
- `pages/TokenPage.tsx` — Breadcrumb, success banner, on-chain data, tx hash from `sessionStorage`, clickable metadata URL

**Demo mode vs live mode:**

| | Demo mode | Live mode |
|-|-----------|-----------|
| Trigger | Middleware unreachable (2s timeout) | Middleware responds OK on `/api/assets` |
| Tokens shown | 4 mock tokens (PROP, NFT1, INV1, GOV1) | Real tokens from wallet-rpc |
| Create button | Disabled — shows full setup instructions | Enabled |
| Token status | "Sample data (not on chain)" | "Confirmed on chain" |
| Status pill | Yellow "Demo" | Green "Live" |

The connection check result is cached (`_connected`) — runs once per page load.

**Transaction hash persistence:**
After creation, `CreatePage` stores `{tx_hash, fee, ticker}` in `sessionStorage` key `salvium_last_tx`. `TokenPage` reads this when `?created=1` is in the URL and displays the hash and fee. Cleared on next page session.

**GitHub Pages deployment:**
- Uses `HashRouter` — all routes are `/#/`, `/#/create`, `/#/token/PROP`. Works on static hosting with no server-side routing.
- `vite.config.ts` sets `base: '/salvium-demo/'` (must match GitHub repo name).
- `VITE_API_URL` env var sets middleware URL. Defaults to `http://127.0.0.1:3001`.

**Build tooling issues resolved:**

| Problem | Cause | Fix |
|---------|-------|-----|
| Vite 8 fails to start | Requires Node 20.19+; system has 20.18.0 | Downgraded to `vite@5` |
| Tailwind PostCSS error | Tailwind v4 moved PostCSS plugin to `@tailwindcss/postcss` | Downgraded to `tailwindcss@3` |
| PostCSS config not loading | `"type":"module"` in `package.json` causes `.js` config files to be treated as ESM; PostCSS loads config as CommonJS | Renamed `postcss.config.js` → `postcss.config.cjs` and `tailwind.config.js` → `tailwind.config.cjs`, changed `export default` to `module.exports` |

**Branding (aligned to salvium.io):**
- Background: `#1e1e1e` (matches site)
- Primary: `#00bfa5`, primary-dark: `#009688`
- Fonts: Inter (body) + Josefin Sans (headings) — same as salvium.io
- Border radius: 4px flat throughout
- Primary button: `linear-gradient(135deg, #00bfa5, #009688)` with `translateY(-2px)` hover
- Cards: `linear-gradient(to bottom, rgba(40,40,40,0.95), rgba(30,30,30,0.98))`
- Logo: Salvium wordmark SVG copied from `site/images/optimized/salvium_wordmark_white_1024x1024px.svg`, sized `w-24` (96px) to match the main site header

---

## 8. Bugs & Issues in wallet-rpc RC2 — For Neil

Tested 2026-03-12 against `v1.1.0-RC2`.

### Bug 1 — Metadata fields silently dropped (HIGH)

**Affected params:** `url`, `hash`, `size`, `token_metadata_hex`

All four are accepted by `create_token` without error but are not written to the transaction.

**Confirmed by:** Fetching the raw transaction via the daemon's `get_transactions` endpoint and inspecting the `extra` field. A minimal token (ticker + name + supply only) and a full-metadata token (with `token_metadata_hex`, `hash`, `size`, `url` all populated) both produce an `extra` field of exactly **44 bytes**. If metadata were stored, the extra field would be larger.

**Impact:** `get_token_info` always returns `url: ""` and `size: 0`. The Tier 2 metadata blob is not retrievable from the chain in RC2.

**Workaround:** SDK and middleware accept and pass these params as normal so the code path is correct and ready — they will work when the implementation is completed.

---

### Bug 2 — `get_tokens` errors on empty wallet (LOW)

Returns:
```json
{"error":{"code":-1,"message":"Failed to get token data from wallet."}}
```
when the wallet has no tokens, instead of:
```json
{"result":{"tokens":[]}}
```

**Impact:** Client code that checks for `result.tokens` gets `undefined` and crashes.

**Workaround:** SDK catches any error containing `"Failed to get token data"` and returns `[]`. Middleware inherits this via the SDK.

---

### Question 3 — `hash` field format (UNRESOLVED)

The `hash` field passed to `create_token` — should it be a bare SHA-256 hex string (e.g. `a207fd04...`) or `0x`-prefixed (`0xa207fd04...`)?

Cannot verify by reading it back via `get_token_info` (returns empty in RC2). The SDK currently passes **bare hex** (no prefix) to be consistent with `token_metadata_hex`.

The Meta Generator's `computeHash()` in `hashing.ts` returns a `0x`-prefixed hash — this needs to be confirmed and fixed once the RPC can return the stored hash for comparison.

---

### Question 4 — `ticker` character set (UNRESOLVED)

Confirmed: exactly 4 characters required. Uppercase letters + digits tested and working.

Not confirmed: are lowercase letters valid? Are symbols (e.g. `-`, `_`) valid? Are non-ASCII characters valid?

---

### Info 5 — Mining API inconsistency (LOW)

`start_mining` and `stop_mining` are plain HTTP endpoints on port `29082` (`/start_mining`), not `json_rpc` methods.

All other operations use `json_rpc` on port `29081`. Worth noting in docs as a footgun for developers who assume all daemon methods are JSON-RPC.

---

## 9. Meta Generator — Bugs Fixed

**Location:** `salvium/meta-generator/src/`

| # | File | Bug | Fix applied |
|---|------|-----|-------------|
| 1 | `hooks/useMetadataForm.ts` line ~100 | `getCliCommand()` called `generateCliCommand({ hexMetadata: '0x' + hexPayload })` — prepended `0x` prefix, producing hex that fails RPC validation | Removed `'0x' +` — `toHex()` in `hashing.ts` already returns bare hex |
| 2 | `utils/generateCliCommand.ts` | Used method name `create_asset` — wrong | Changed to `create_token` |
| 3 | `utils/hexEncoding.ts` | Duplicate `toHex()` that returned `0x`-prefixed hex — contradicted the correct implementation in `hashing.ts`. Unused but a landmine for future developers | **File deleted** |
| — | `utils/hashing.ts` `computeHash()` | Returns `0x`-prefixed SHA-256. Whether `create_token`'s `hash` param expects this is **unverified** (see Question 3 above) | Not changed pending Neil's confirmation |

---

## 10. Knowledge Base

**Location:** `salvium/docs/docs/TOKENS/`
**10 pages, all written. Added to `mkdocs.yml` nav as "Tokens & RWA" tab.**

| File | Content |
|------|---------|
| `index.md` | Overview, token type table, links to all sections |
| `token-protocol.md` | Three-tier architecture, field tables, on-chain flow |
| `token-types.md` | Full JSON examples for NFT, RWA, Invoice, Governance, Compliance |
| `getting-started.md` | Step-by-step: daemon → wallet-rpc → fund → create_token → confirm |
| `metadata-schema.md` | Complete Tier 2/3 field reference |
| `meta-generator.md` | Tool usage, encoding details, known fixed bugs |
| `demo-ui.md` | Demo vs live mode, three screens, local stack, GitHub Pages deploy |
| `sdk.md` | Full TypeScript SDK reference with types |
| `middleware.md` | REST endpoints, env vars, CORS, deployment options |
| `rpc-reference.md` | All wallet-rpc token methods with params, errors, RC2 limitations table |

---

## 11. Open Questions for Neil

| # | Question | Why it matters |
|---|----------|----------------|
| 1 | Does `hash` in `create_token` (and inside the Tier 1 JSON) expect bare hex or `0x`-prefixed? | `hashing.ts` in the Meta Generator returns `0x`-prefixed. SDK currently passes bare hex. One of them is wrong. |
| 2 | When will `token_metadata_hex` (and the individual `url`/`hash`/`size` params) actually be stored on-chain? | The entire Tier 2 architecture depends on this. All code paths are in place — just needs the RPC implementation to complete it. |
| 3 | Are lowercase letters or symbols valid in `ticker`? | Docs say 4 characters — is it uppercase alphanumeric only? |
| 4 | Is there a planned block explorer for the testnet? | UI currently says "not available for RC2". |
| 5 | Is `token_type: 2` the permanent identifier for user-created tokens? | Documented in UI as "SAL Token (type 2)". |
| 6 | Are the individual `name`, `url`, `hash`, `size` params to `create_token` redundant with what's inside `token_metadata_hex`, or are they the authoritative source? | Affects how we validate and what we pass. |

---

## 12. Correction: `token_metadata_hex` Encodes Tier 1, Not Tier 2

**Reported by Neil, 2026-03-13.**

### What we built (wrong)

The SDK's `createToken()` takes a `metadata` object (the full Tier 2 JSON), serialises it to compact JSON, and hex-encodes that into `token_metadata_hex`:

```typescript
// WRONG — current SDK behaviour
const compact = JSON.stringify(tier2MetadataObject);
rpcParams.token_metadata_hex = Buffer.from(compact, 'utf8').toString('hex');
```

This encodes the Tier 2 blob directly into the `token_metadata_hex` field.

### What it should do (correct)

`token_metadata_hex` should contain hex-encoded **Tier 1 JSON** — a small JSON object pointing to the Tier 2 blob:

```json
{
  "name": "123 High Street Property Token",
  "url": "https://your-server.com/metadata/PROP.json",
  "hash": "f0a7f0ef2d93750f...",
  "size": 387
}
```

The Tier 2 blob (the full metadata with `technical{}`, `rwa{}`, `nft{}`, etc.) lives off-chain at `url` and is not passed to `create_token` directly.

### Correct encoding flow (to implement)

```
Input:
  - name          (string)
  - tier2Metadata (object — full Tier 2 JSON)
  - url           (string — where Tier 2 is hosted)

Steps:
  1. compact = JSON.stringify(tier2Metadata)          // compact UTF-8
  2. hash    = sha256(compact)                         // hex, format TBC (bare or 0x?)
  3. size    = Buffer.byteLength(compact, 'utf8')
  4. tier1   = JSON.stringify({ name, url, hash, size })
  5. token_metadata_hex = Buffer.from(tier1).toString('hex')   // no 0x prefix

create_token params:
  ticker, supply, name,
  token_metadata_hex,   // hex(tier1JSON)
  url,                  // also a direct param
  hash,                 // also a direct param
  size                  // also a direct param
```

### Files that need updating

| File | What needs to change |
|------|---------------------|
| `asset-sdk/src/index.ts` | `createToken()` — encode Tier 1 JSON into `token_metadata_hex`, not Tier 2 metadata. The `metadata` param becomes input for computing `hash`/`size`, not for encoding. The `url` param becomes required (or at least meaningful). |
| `middleware/src/server.ts` | No logic change needed — passes params through to SDK. |
| `demo-ui/src/pages/CreatePage.tsx` | Currently passes `metadata` object but no `url`. Will need a hosted URL or at least a placeholder for the Tier 2 blob. |
| `docs/docs/TOKENS/token-protocol.md` | Architecture diagram and Tier 1/2 description. |
| `docs/docs/TOKENS/rpc-reference.md` | `token_metadata_hex` param description. |
| `docs/docs/TOKENS/sdk.md` | `createToken()` encoding description. |
| `docs/docs/TOKENS/meta-generator.md` | "What it produces" and encoding steps. |
| `docs/docs/TOKENS/middleware.md` | Request body description for `POST /api/assets`. |

### Blocker

The SDK fix requires knowing:
- The exact Tier 1 JSON field names and structure (confirmed: `name`, `url`, `hash`, `size`)
- Whether `hash` is bare hex or `0x`-prefixed inside the Tier 1 JSON (Question 1 above)
- Whether `url` can be empty/omitted if the Tier 2 blob is not yet hosted

**Do not update SDK or docs beyond this note until Question 1 is answered.**
