
## 🔑 Wallet Address Changes (Expanded for Miners & Pools)

After the Salvium One hard fork at block **334750 (October 13th, 2025 ~2pm GMT)**:

* Every wallet will now display **two addresses** when opened:

  * **Legacy (CryptoNote)** address (starts with `SaLv...`)
  * **Carrot (post-fork)** address (starts with `SC1...`)

⚠️ Only **Carrot addresses** will be valid for mining and payouts after the fork. Legacy addresses will stop working for new transactions.

### How to find your Carrot address

1. **CLI Wallet v1.0.x**

   * Open your wallet with the new binary.
   * You’ll see both addresses printed (see screenshot below).
   * The line `Opened Carrot wallet: SC1...` is your new valid address.

2. **CLI command (if not shown automatically)**

   * Use:

     ```
     address 0
     ```
   * This will print your primary address. It will show both the old and new formats (see screenshot).

3. **If using RPC**

   * Starting with **v1.0.1** (next week), you can use the new `get_address` RPC call.
   * Example request:

     ```json
     {"jsonrpc":"2.0","id":"0","method":"get_address","params":{"account_index":0}}
     ```
   * Response will include both legacy and Carrot addresses.

---

## 🛠️ Guidance for Miners

* **Update mining config (xmrig or equivalent)** to use your **Carrot address** (`SC1...`) **before the fork height**.
* If you continue mining with a legacy address after the fork, shares will be rejected.
* Pools may allow you to add both old and new addresses before the fork for smoother transition.

---

## 🛠️ Guidance for Pools

* **Switch pool payout wallets** to Carrot format at the fork height.
* For each miner:

  * Collect their Carrot addresses in advance (recommended).
  * Or flush old balances to legacy addresses before the fork to avoid stranded funds.
* Pools should update **regex validation** to reject legacy addresses at height 334750 and only accept Carrot thereafter.

---

## ❓ Updated FAQ

**Q: How do I find my Carrot address?**
A: Open the CLI wallet v1.0.x. The new Carrot address (`SC1...`) is shown when you log in. If not, use the command `address 0`. RPC users can call `get_address` (v1.0.1+).

**Q: Can I keep mining with my old address after the fork?**
A: No. Mining to legacy addresses will fail after block 334750. You must switch to your Carrot address.

**Q: I’m a pool operator. How do I handle unpaid balances?**
A: You can:

* Flush pending balances to legacy addresses before the fork, or
* Collect new Carrot addresses from miners and map them in your DB.

**Q: What happens if I forget to update my mining software?**
A: Shares submitted with legacy addresses will be rejected post-fork. Update your config to Carrot addresses as soon as possible.
