# wallet-rpc Reference

This page documents the wallet-rpc methods used for token operations. All calls use JSON-RPC 2.0 over HTTP with Digest authentication.

**Base URL:** `http://127.0.0.1:29088/json_rpc`

**Authentication:** None required when wallet-rpc is started without `--rpc-login` (recommended for testnet). If you do use `--rpc-login`, add `-u <user>:<pass> --digest` to every curl command.

---

## Connection example

```bash
curl http://127.0.0.1:29088/json_rpc \
  -d '{"jsonrpc":"2.0","id":"0","method":"<method>","params":{}}' \
  -H 'Content-Type: application/json'
```

---

## Token methods

### `create_token`

Creates a new token on the chain. Requires chain height > 1200 and sufficient unlocked balance.

**Parameters:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `ticker` | string | Yes | Token ticker — **must be exactly 4 characters** |
| `supply` | number | Yes | Total token supply |
| `name` | string | No | Display name |
| `token_metadata_hex` | string | No | Hex-encoded **Tier 1 JSON**: `{"name":"...","url":"...","hash":"...","size":N}` — **no `0x` prefix** |
| `url` | string | No | URI to hosted metadata blob |
| `hash` | string | No | SHA-256 hex of the metadata blob |
| `size` | number | No | Byte length of the metadata blob |
| `account_index` | number | No | Wallet account index (default: 0) |
| `get_tx_keys` | boolean | No | Return transaction keys |
| `do_not_relay` | boolean | No | Build transaction without broadcasting |
| `get_tx_hex` | boolean | No | Return raw transaction hex |

**Example — minimal:**

```bash
curl http://127.0.0.1:29088/json_rpc \
  -d '{
    "jsonrpc": "2.0",
    "id": "0",
    "method": "create_token",
    "params": {
      "ticker": "TST1",
      "supply": 1,
      "name": "Test Token"
    }
  }' \
  -H 'Content-Type: application/json'
```

**Example — with metadata:**

```bash
curl http://127.0.0.1:29088/json_rpc \
  -d '{
    "jsonrpc": "2.0",
    "id": "0",
    "method": "create_token",
    "params": {
      "ticker": "PROP",
      "supply": 1,
      "name": "123 High Street Property Token",
      "token_metadata_hex": "7b226e616d65223a...",
      "hash": "f0a7f0ef2d93750f...",
      "size": 387
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

**Error responses:**

| Code | Message | Cause |
|------|---------|-------|
| -7 | `Create_token is not available yet.` | Chain height below 1200 |
| -7 | `Asset type must be exactly 4 characters long.` | Ticker is not exactly 4 chars |
| -1 | `Failed to parse metadata hex` | `token_metadata_hex` has `0x` prefix or invalid hex |

---

### `get_tokens`

Returns all token tickers held in the wallet.

```bash
curl http://127.0.0.1:29088/json_rpc \
  -d '{"jsonrpc":"2.0","id":"0","method":"get_tokens"}' \
  -H 'Content-Type: application/json'
```

**Response:**

```json
{
  "result": {
    "tokens": ["salPROP", "salNFT1", "salTST1"]
  }
}
```

---

### `get_token_info`

Returns on-chain information for a single token.

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

!!! note "`asset_type` uses the `sal` prefix"
    Pass `salPROP`, not `PROP`. The response returns `asset_type` without the prefix.

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

---

## Standard wallet methods

These standard wallet-rpc methods also work with token wallets:

### `getbalance`

```bash
curl http://127.0.0.1:29088/json_rpc \
  -d '{"jsonrpc":"2.0","id":"0","method":"getbalance","params":{"account_index":0}}' \
  -H 'Content-Type: application/json'
```

Returns balances for all asset types held in the wallet, including SAL1 and any custom tokens.

### `get_height`

```bash
curl http://127.0.0.1:29088/json_rpc \
  -d '{"jsonrpc":"2.0","id":"0","method":"get_height"}' \
  -H 'Content-Type: application/json'
```

### `refresh`

Forces the wallet to sync with the daemon.

```bash
curl http://127.0.0.1:29088/json_rpc \
  -d '{"jsonrpc":"2.0","id":"0","method":"refresh"}' \
  -H 'Content-Type: application/json'
```

---

## Daemon HTTP endpoints

These endpoints are on the daemon (not wallet-rpc) and use plain HTTP (no auth):

```bash
# Start mining
curl http://127.0.0.1:29082/start_mining \
  -d '{"do_background_mining":false,"ignore_battery":true,"miner_address":"<address>","threads_count":1}' \
  -H 'Content-Type: application/json'

# Stop mining
curl http://127.0.0.1:29082/stop_mining -H 'Content-Type: application/json'

# Get chain info (JSON-RPC)
curl http://127.0.0.1:29081/json_rpc \
  -d '{"jsonrpc":"2.0","id":"0","method":"get_info"}' \
  -H 'Content-Type: application/json'
```

!!! info "Two daemon ports"
    The testnet daemon binds two ports: `29081` for JSON-RPC (`/json_rpc`) and `29082` for HTTP endpoints (`/start_mining`, `/stop_mining`, etc.).

