# Salvium One Hard Fork – Complete Guide

### Overview

Salvium One, the most significant upgrade in our project's history, successfully activated at block 334,750 on October 13th, 2024 at approximately 2pm GMT. This transformative hard fork has delivered the foundation for a **compliant privacy ecosystem**.

**Current Network Status:**
* **Fork Block:** 334,750 (Successfully mined October 13th, 2024)
* **Current Required Version:** v1.0.5 or higher
* **Network Status:** Fully operational with Carrot addresses active

⚠️ **Action Required:** If you haven't upgraded yet, please update to latest version: [Download Latest Version](https://salvium.io/download)

---

## What is Salvium One?

Salvium One is the culmination of 15 months of development, designed to deliver:

* **Privacy-first transactions** compatible with regulatory compliance
* **Carrot address format** – a new address system required for future features
* **SPARC integration** – private, anonymised return payments
* **Full DeFi readiness** – foundations for private tokens, DEXs, and lending protocols

---

## Key Features

### Carrot Address System

* New address prefix: `SC1...`
* Enables refundable transactions without revealing sender addresses
* Critical for exchange compliance and interoperability
* Legacy CryptoNote addresses (`SaLv...`) become **invalid at fork**

### SPARC Integration

* Private refund mechanism for exchanges
* Allows returns of unauthorized deposits without exposing transaction history

### T-CLSAG

* Updated version of Monero’s CLSAG ring signatures
* Maintains anonymity while enabling Carrot address support

### View-Only Wallets

* Full visibility of **incoming and outgoing transactions**
* Meets compliance and audit requirements

---

## ⚠️ Critical Change: Wallet Address Migration

### What Happens at the Fork

* Your wallet will automatically generate a **Carrot address** (`SC1...`)
* Your old CryptoNote address (`SaLv...`) becomes a **legacy address**
* After the fork, only Carrot addresses will be accepted for mining and payouts

Balances remain untouched. You **do not** need to move your coins — only update your software and addresses.

---

### How to Find Your Carrot Address

**1. CLI Wallet v1.0.0 and above**
Open your wallet and you’ll see both the legacy and Carrot addresses displayed, for example:

```
Opened legacy (CN) wallet: SaLvTyKva...
Opened Carrot wallet: SC1TotkUC9...
```

**2. CLI Command**
Run:

```
address 0
```

This will display your primary address in both formats.

**3. RPC (from v1.0.1 onward)**
Use the `get_address` RPC call:

```json
{"jsonrpc":"2.0","id":"0","method":"get_address","params":{"account_index":0}}
```

---

## 🛠️ Guidance for Miners

* Update your miner config (xmrig, etc.) with your **Carrot address** before the fork
* If you continue mining with a legacy address after block 334750, shares will be rejected
* If supported by your pool, you may enter both old and new addresses during the pre-fork period to ensure a smooth switch

---

## 🛠️ Guidance for Pools

* Update your pool payout wallet to a Carrot address before the fork
* Accept Carrot addresses only after block 334750. Legacy addresses must be rejected
* Manage unpaid balances:

  * Flush outstanding balances to legacy addresses before the fork, **or**
  * Collect Carrot addresses from miners in advance and map `{old → new}` in your DB

**Regex patterns for validation:**

* Legacy:

  ```
  ^(SaLv)[1-9A-HJ-NP-Za-km-z]{94,140}$
  ```

* Carrot:

  ```
  ^(SC1)[1-9A-HJ-NP-Za-km-z]{94,140}$
  ```

---

## ❓ Frequently Asked Questions

**Will my balance change after the fork?**
No. Your coins and balances remain the same. Only the wallet address format changes.

**Do I need to move coins before the fork?**
No. You don’t need to move anything. Just update your wallet software.

**How can I find my new address?**
Open v1.0.0+ of the CLI wallet, or run `address 0`. RPC users can call `get_address` from v1.0.1.

**What happens if I mine to my old address after the fork?**
Your shares will be rejected. Pools will not accept legacy addresses post-fork.

**I’m a pool operator. How do I handle unpaid balances?**
Either flush them to old addresses before the fork or collect new Carrot addresses in advance.

**Does this affect staking or SPARC?**
Yes. Both require Carrot addresses. Make sure your infrastructure is updated.

---

## 📚 Resources

* [GitHub v1.0.0 Binaries](https://github.com/salvium/salvium/releases/tag/v1.0.0)
* [Introducing Salvium One Blog Post](https://salvium.io/blog/2025/09/30/sal-one/)
* [Explorer](https://explorer.salvium.io/)
* [Salvium One White Paper (PDF)](https://github.com/salvium/salvium_library/blob/main/papers/Salvium_One_White_Paper_v1.pdf)
* [Join the Community on Discord](https://discord.gg/gvbyNQQ86p)

---

✅ Salvium One is not just a hard fork — it’s the beginning of a new era for private, compliant DeFi. Update your binaries, switch to your Carrot address, and be ready for October 13th, 2025.


