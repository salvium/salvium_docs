# Getting Started

!!! warning "Testnet preview — not tested"
    This guide uses the **Salvium RC2 testnet**. The token tooling has not been tested.
    Treat this as a developer preview — suitable for exploration and feedback, not production use.

This guide walks you through setting up a local Salvium testnet and minting your first token from scratch.

---

## Prerequisites

- **Salvium binaries** — `salviumd.exe`, `salvium-wallet-cli.exe`, `salvium-wallet-rpc.exe`
- **Node.js 20+**
- **A terminal** — CMD or PowerShell on Windows, bash on Linux/macOS

---

## Step 1 — Start the testnet daemon

Open a terminal and run:

```bash
salviumd --testnet --offline --fixed-difficulty 500
```

Wait until you see `SYNCHRONIZED OK`. The daemon is now running a local private testnet.

!!! info "Port reference"
    The testnet daemon binds to:

    - `127.0.0.1:29081` — JSON-RPC
    - `127.0.0.1:29082` — HTTP endpoints (mining)

---

## Step 2 — Create a wallet

In a new terminal:

```bash
salvium-wallet-cli --testnet --generate-new-wallet ./demo --password demo
```

Once the address is shown, type `exit`.

---

## Step 3 — Start the wallet RPC server

```bash
salvium-wallet-rpc \
  --wallet-file ./demo \
  --password demo \
  --testnet \
  --daemon-address 127.0.0.1:29081 \
  --rpc-bind-port 29088 \
  --disable-rpc-login
```

You should see:

```
Binding on 127.0.0.1 (IPv4):29088
Starting wallet RPC server
```

!!! warning "Do not use `--offline` on the wallet-rpc"
    The daemon runs offline, but the wallet-rpc must connect to the daemon. Only pass `--offline` to `salviumd`, not to `salvium-wallet-rpc`.

---

## Step 4 — Fund your wallet

Token creation requires confirmed funds and a chain height above block **1200**.

Mine some blocks to your wallet address:

```bash
# Replace <YOUR_ADDRESS> with the address from Step 2
curl http://127.0.0.1:29082/start_mining \
  -d '{"do_background_mining":false,"ignore_battery":true,"miner_address":"<YOUR_ADDRESS>","threads_count":1}' \
  -H 'Content-Type: application/json'
```

Wait until the chain height passes 1200, then stop mining:

```bash
curl http://127.0.0.1:29082/stop_mining -H 'Content-Type: application/json'
```

Check your balance:

```bash
curl http://127.0.0.1:29088/json_rpc \
  -d '{"jsonrpc":"2.0","id":"0","method":"getbalance"}' \
  -H 'Content-Type: application/json'
```

You should see a non-zero `unlocked_balance`.

!!! tip "Keep your UTXO set small"
    If you mine thousands of blocks to one wallet, transaction building becomes very slow due to the large number of unspent outputs. For testing, mine just enough blocks to get a comfortable balance (~100–200 blocks past 1200 is ideal).

---

## Step 5 — Create your first token

```bash
curl http://127.0.0.1:29088/json_rpc \
  -d '{
    "jsonrpc": "2.0",
    "id": "0",
    "method": "create_token",
    "params": {
      "ticker": "TST1",
      "supply": 1,
      "name": "My First Token"
    }
  }' \
  -H 'Content-Type: application/json'
```

You'll get back a `tx_hash_list` confirming the transaction was broadcast.

!!! warning "Ticker must be exactly 4 characters"
    `TST` (3 chars) or `TEST1` (5 chars) will fail with an error. Use exactly 4 alphanumeric characters.

---

## Step 6 — Confirm and list your tokens

Mine a few more blocks to confirm the transaction:

```bash
curl http://127.0.0.1:29082/start_mining \
  -d '{"do_background_mining":false,"ignore_battery":true,"miner_address":"<YOUR_ADDRESS>","threads_count":1}' \
  -H 'Content-Type: application/json'
# wait ~10 seconds
curl http://127.0.0.1:29082/stop_mining -H 'Content-Type: application/json'
```

Then list your tokens:

```bash
curl http://127.0.0.1:29088/json_rpc \
  -d '{"jsonrpc":"2.0","id":"0","method":"get_tokens"}' \
  -H 'Content-Type: application/json'
```

Response:

```json
{
  "result": {
    "tokens": ["salTST1"]
  }
}
```

---

## Step 7 — Create a token with rich metadata

Use the [Meta Generator](meta-generator.md) to build your metadata, or construct it manually:

```bash
# Build the Tier 2 JSON blob (host this at your URL after creation)
TIER2='{"name":"My Property Token","description":"Test RWA","id":"TEST-001","technical":{"standard":"ERC-3643","encoding":{"charset":"UTF-8"}},"created_at":"2026-03-12T00:00:00Z","schema_version":"2.0.0"}'

# Compute hash and byte size of the Tier 2 blob (Linux/macOS)
HASH=$(printf '%s' "$TIER2" | sha256sum | cut -d' ' -f1)
SIZE=$(printf '%s' "$TIER2" | wc -c | tr -d ' ')

# Pass the Tier 1 fields individually
curl http://127.0.0.1:29088/json_rpc \
  -H 'Content-Type: application/json' \
  -d "{
    \"jsonrpc\": \"2.0\",
    \"id\": \"0\",
    \"method\": \"create_token\",
    \"params\": {
      \"ticker\": \"PROP\",
      \"supply\": 1,
      \"name\": \"My Property Token\",
      \"url\": \"https://your-server.com/metadata/PROP.json\",
      \"hash\": \"$HASH\",
      \"size\": $SIZE
    }
  }"
```

!!! tip "Two ways to pass Tier 1 metadata"
    The example above passes `name`, `url`, `hash`, and `size` as individual fields — the simplest approach in a shell script. Alternatively, you can build the Tier 1 JSON object `{"name":"...","url":"...","hash":"...","size":N}`, hex-encode it, and pass the result as `token_metadata_hex`. The SDK does this automatically when you pass a `metadata` object to `createToken()`.

!!! warning "`hash` — no `0x` prefix"
    Pass the raw SHA-256 hex without a `0x` prefix.

---

## Next steps

- Use the [Meta Generator](meta-generator.md) for a graphical way to build and export metadata
- Use the [Demo UI](demo-ui.md) for the full end-to-end experience
- Use the [SalviumAssetSDK](sdk.md) to integrate token creation into your own application
