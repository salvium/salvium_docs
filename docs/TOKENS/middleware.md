# Middleware API

The **Salvium middleware** is a lightweight Express.js server that sits between your application and the Salvium wallet-rpc. It handles Digest authentication, CORS, and exposes a clean REST API.

---

## Why middleware?

Browsers cannot make direct Digest-authenticated requests to the wallet-rpc (the standard `fetch` API doesn't support Digest auth, and CORS headers are not set by the wallet-rpc binary). The middleware solves both problems.

```
Browser / App
    ↓  plain HTTP POST (no auth)
Middleware  :3001
    ↓  HTTP Digest auth
wallet-rpc  :29088
    ↓
Salvium chain
```

---

## Running the middleware

```bash
cd salvium/middleware

RPC_URL=http://127.0.0.1:29088/json_rpc \
RPC_USER=1 \
RPC_PASS=1 \
PORT=3001 \
npx tsx src/server.ts
```

On startup you'll see:

```
Salvium middleware running on http://127.0.0.1:3001
  → wallet-rpc: http://127.0.0.1:29088/json_rpc
```

---

## Environment variables

| Variable | Default | Description |
|----------|---------|-------------|
| `RPC_URL` | `http://127.0.0.1:29088/json_rpc` | wallet-rpc JSON-RPC endpoint |
| `RPC_USER` | *(empty)* | Digest auth username |
| `RPC_PASS` | *(empty)* | Digest auth password |
| `PORT` | `3001` | Port the middleware listens on |

If `RPC_USER` and `RPC_PASS` are empty, no Digest auth is attempted (suitable for a wallet-rpc started with `--disable-rpc-login`).

---

## Endpoints

### `GET /api/assets`

Returns all token tickers in the wallet.

**Response:**

```json
{
  "tokens": ["salPROP", "salNFT1", "salTST1"]
}
```

**Error (no tokens in wallet):**

```json
{
  "tokens": []
}
```

---

### `GET /api/assets/:ticker`

Returns on-chain info for a single token.

**Example:** `GET /api/assets/PROP`

**Response:**

```json
{
  "asset_type": "PROP",
  "name": "123 High Street Property Token",
  "supply": 1,
  "url": "",
  "token_type": 2,
  "version": 1
}
```

The `:ticker` parameter accepts either the raw ticker (`PROP`) or the `sal`-prefixed form (`salPROP`).

---

### `POST /api/assets`

Creates a new token on the Salvium chain.

**Request body:**

```json
{
  "ticker": "PROP",
  "supply": 1,
  "name": "123 High Street Property Token",
  "metadata": {
    "name": "123 High Street Property Token",
    "description": "Fractional ownership token.",
    "id": "GB-PROP-2026-0001",
    "technical": {
      "standard": "ERC-3643",
      "encoding": { "charset": "UTF-8" }
    },
    "created_at": "2026-03-12T00:00:00Z",
    "schema_version": "2.0.0"
  },
  "url": "https://your-server.com/metadata/PROP.json"
}
```

| Field | Required | Description |
|-------|----------|-------------|
| `ticker` | Yes | Exactly 4 characters |
| `supply` | Yes | Token supply |
| `name` | No | Display name |
| `metadata` | No | Tier 2 metadata object — middleware handles encoding |
| `url` | No | URI to hosted metadata |

**Response:**

```json
{
  "tx_hash": "2ef96b5f128ad437367ed076775eddfb...",
  "fee": 10521160,
  "hex_payload": "7b226e616d65223a...",
  "hash": "f0a7f0ef2d93750fbb4928e8adafb129...",
  "size": 387
}
```

| Field | Description |
|-------|-------------|
| `tx_hash` | Transaction hash — use to track confirmation |
| `fee` | Fee paid in atomic units (divide by 1e12 for SAL) |
| `hex_payload` | Hex-encoded metadata (no `0x` prefix) |
| `hash` | SHA-256 of the compact metadata JSON |
| `size` | Byte length of the compact metadata JSON |

---

## CORS

The middleware allows all origins (`Access-Control-Allow-Origin: *`) and all `Content-Type` headers. This is appropriate for a local development/demo server. For production deployments, restrict the origin to your domain.

---

## Deploying the middleware

The middleware is a standard Node.js Express app. It can be deployed to any platform that runs Node:

=== "Railway"

    ```bash
    railway init
    railway up
    ```

    Set `RPC_URL`, `RPC_USER`, `RPC_PASS` as environment variables in the Railway dashboard.

=== "Render"

    1. Connect your GitHub repo
    2. Set build command: `npm install && npm run build`
    3. Set start command: `node dist/server.js`
    4. Add environment variables

=== "Fly.io"

    ```bash
    fly launch
    fly secrets set RPC_URL=... RPC_USER=... RPC_PASS=...
    fly deploy
    ```

!!! warning "The wallet-rpc must also be accessible"
    If you deploy the middleware to a remote server, the wallet-rpc must be running on the same server or accessible over the network. Running a Salvium testnet node on a remote server requires outbound connectivity and storage.
