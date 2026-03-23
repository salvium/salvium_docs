# SalviumAssetSDK

The **SalviumAssetSDK** is a TypeScript/Node.js library that wraps the Salvium wallet-rpc for token operations. It handles JSON serialisation, hex encoding, hash computation, and HTTP Digest authentication automatically.

**Package:** `@salvium/asset-sdk`

---

## Installation

```bash
npm install @salvium/asset-sdk
```

---

## Quick start

```typescript
import { SalviumAssetSDK } from '@salvium/asset-sdk';

// Direct to wallet-rpc (requires Digest auth)
const sdk = new SalviumAssetSDK({
  rpcUrl: 'http://127.0.0.1:29088/json_rpc',
  username: '1',
  password: '1',
});

// Or via middleware proxy (no auth required)
const sdk = new SalviumAssetSDK({
  rpcUrl: 'http://127.0.0.1:3001',
});
```

---

## API Reference

### `new SalviumAssetSDK(config)`

| Parameter | Type | Description |
|-----------|------|-------------|
| `config.rpcUrl` | `string` | wallet-rpc URL or middleware proxy URL |
| `config.username` | `string?` | Digest auth username (wallet-rpc only) |
| `config.password` | `string?` | Digest auth password (wallet-rpc only) |

---

### `sdk.createToken(params)`

Creates a new token on the Salvium chain.

```typescript
const result = await sdk.createToken({
  ticker: 'PROP',        // exactly 4 characters
  supply: 1,
  name: '123 High Street Property Token',
  metadata: {
    name: '123 High Street Property Token',
    description: 'Fractional ownership token for UK residential property.',
    id: 'GB-PROP-2026-0001',
    technical: {
      standard: 'ERC-3643',
      encoding: { charset: 'UTF-8' },
    },
    created_at: new Date().toISOString(),
    schema_version: '2.0.0',
    rwa: {
      asset_type: 'real_estate',
      jurisdiction: 'GB',
      valuation: { amount: 250000, currency: 'GBP', date: '2026-01-15' },
    },
  },
  url: 'https://your-server.com/metadata/PROP.json',  // optional
});

console.log(result.tx_hash);     // transaction hash
console.log(result.hex_payload); // hex-encoded Tier 1 JSON (what was passed to token_metadata_hex)
console.log(result.hash);        // SHA-256 of the Tier 2 blob (for hosting/verification)
console.log(result.size);        // byte length of the Tier 2 blob
console.log(result.fee);         // fee in atomic units
```

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `ticker` | `string` | Yes | Exactly 4 characters |
| `supply` | `number` | Yes | Total token supply |
| `name` | `string` | No | Display name |
| `metadata` | `TokenMetadata` | No | Tier 2 metadata object — SDK handles encoding |
| `url` | `string` | No | URI to hosted metadata blob |

**The SDK automatically:**

1. Serialises the `metadata` (Tier 2) to compact UTF-8 JSON
2. Computes SHA-256 `hash` and byte `size` of the Tier 2 JSON
3. Builds the Tier 1 JSON: `{ "name", "url", "hash", "size" }`
4. Hex-encodes the Tier 1 JSON (no `0x` prefix) → `token_metadata_hex`
5. Calls `create_token` with all parameters

---

### `sdk.listTokens()`

Returns all token tickers held in the wallet.

```typescript
const tickers = await sdk.listTokens();
// ['salPROP', 'salNFT1', 'salTST1']
```

Returns an empty array if the wallet holds no tokens.

---

### `sdk.getToken(ticker)`

Returns on-chain info for a single token.

```typescript
const token = await sdk.getToken('PROP');
// or
const token = await sdk.getToken('salPROP');  // sal prefix is optional

console.log(token.asset_type); // 'PROP'
console.log(token.name);       // '123 High Street Property Token'
console.log(token.supply);     // 1
console.log(token.token_type); // 2
console.log(token.version);    // 1
```

---

## TypeScript types

```typescript
interface SDKConfig {
  rpcUrl: string;
  username?: string;
  password?: string;
}

interface TokenMetadata {
  name: string;
  description?: string;
  id?: string;
  technical: {
    standard: string;
    encoding: { charset: string };
  };
  created_at: string;
  schema_version: string;
  [key: string]: unknown;  // allows rwa{}, governance{}, etc.
}

interface CreateTokenParams {
  ticker: string;
  supply: number;
  name?: string;
  metadata?: TokenMetadata;
  url?: string;
}

interface CreateTokenResult {
  tx_hash: string;
  fee: number;
  hex_payload: string;
  hash: string;
  size: number;
}

interface TokenInfo {
  asset_type: string;
  name: string;
  supply: number;
  url: string;
  token_type: number;
  version: number;
}
```

---

## Authentication

When `username` and `password` are provided, the SDK uses **HTTP Digest authentication** (the same scheme as curl's `--digest` flag). This is required for direct wallet-rpc access.

When connecting via the [middleware proxy](middleware.md), no authentication is needed from the SDK — the middleware handles auth to wallet-rpc server-side.

---

## Error handling

All methods throw an `Error` with the RPC error message on failure:

```typescript
try {
  await sdk.createToken({ ticker: 'TOOLONG', supply: 1, name: 'Test' });
} catch (err) {
  console.error(err.message);
  // 'ticker must be exactly 4 characters (got "TOOLONG")'
  // or RPC error: 'Asset type must be exactly 4 characters long.'
}
```

Ticker length is validated client-side before the RPC call.
