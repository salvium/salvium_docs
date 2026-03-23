# Tokens & Real-World Assets

!!! warning "Testnet preview"
    The token system — including the Demo UI, Meta Generator, SDK, and middleware — runs against the **Salvium RC2 testnet** and has not been tested.
    All tooling is provided for exploration and developer feedback. Do not use with real assets.

Salvium supports the creation of on-chain tokens representing anything from NFT art to real estate, invoices, and governance proposals — all with the privacy guarantees of the Salvium protocol.

This section explains how the token system works, how to create tokens, and how to build applications on top of it.

---

## What can you tokenise?

| Token type | Standard | Example |
|------------|----------|---------|
| **NFT / Collectible** | ERC-721 | Digital art, certificates, unique items |
| **Real-World Asset** | ERC-3643 | Property, bonds, commodities |
| **Security / Invoice** | ERC-1400 | Invoice finance, equity tokens |
| **Governance** | CIP-108 | DAO proposals, voting tokens |
| **Custom** | Any | Anything you define |

Standards are used as **metadata conventions only** — Salvium is not an EVM chain and has no smart contracts. The standard label tells consuming applications how to interpret the metadata.

---

## How it works — in one sentence

> You fill in a form, the **Meta Generator** produces JSON metadata, the **SDK** encodes it and calls the wallet RPC, and a token appears on the Salvium chain.

---

## Pages in this section

| Page | What it covers |
|------|----------------|
| [Token Protocol](token-protocol.md) | The three-tier architecture — what lives on-chain vs off-chain |
| [Token Types](token-types.md) | NFT, RWA, Invoice, Governance — fields and examples |
| [Getting Started](getting-started.md) | Set up the testnet and mint your first token |
| [Meta Generator](meta-generator.md) | The web tool that builds your metadata |
| [Demo UI](demo-ui.md) | The end-to-end demonstration application |
| [SalviumAssetSDK](sdk.md) | TypeScript SDK for developers |
| [Middleware API](middleware.md) | The REST proxy between your app and the chain |
| [wallet-rpc Reference](rpc-reference.md) | Raw RPC methods: create_token, get_tokens, get_token_info |
| [Metadata Schema](metadata-schema.md) | Full field reference for Tier 2 and Tier 3 metadata |

