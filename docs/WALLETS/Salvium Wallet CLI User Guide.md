# Salvium CLI Wallet User Guide

## 1. Introduction

Welcome to the Salvium CLI (Command Line Interface) wallet guide. This comprehensive document will walk you through every aspect of using the Salvium CLI wallet, from installation to advanced features. Salvium is a privacy-focused cryptocurrency that builds upon the foundations of Monero, adding new features like staking, enhanced privacy, and eventually DeFi capabilities.

## 2. Installation and Setup

### System Requirements

* Operating System: Windows 10/11, macOS 10.14+, or Linux (Ubuntu 18.04+)
* RAM: 4GB minimum, 8GB recommended
* Storage: 50GB+ free space (for full node)
* Internet: Broadband connection

### Download and Install

1. Visit the download page [salvium.io/downloads/](https://salvium.io/downloads/)
2. Choose the appropriate version for your operating system
3. Download binary, unzip and save salvium-wallet-cli.exe in an appropriate folder. Do not use a system folder.

### Antivirus and Firewall settings

Many antivirus programs may flag the Salvium CLI wallet as potentially unwanted software due to its cryptocurrency mining capabilities. This is a false positive, but it can interfere with the wallet's functionality.

1. Add wallet file to Anti Virus exceptions list.
2. Allow salvium-wallet-cli through firewall.
3. For more information see this knowledge base post: [Antivirus and Firewall Settings](https://salvium.io/salvium-knowledge-base/antivirus-and-firewall-settings/)

### First Launch

You do not need to install salvium-wallet-cli.exe, simply run the application from a command prompt or terminal.

1. Open a command prompt/terminal in the folder where you saved the CLI wallet
2. Run the wallet with the command: `salvium-wallet-cli`
3. Follow the prompts to connect to a node (local or remote)
4. Choose between Mainnet, Testnet, or Stagenet with the appropriate flags if needed

## 3. Creating a Wallet

### New Wallet

1. When prompted, type a new wallet name
2. Set a strong password when prompted
3. Write down the displayed 25-word mnemonic seed and keep it safe
4. Confirm that you've saved your seed by typing "YES"

### Restore Wallet

1. Run `salvium-wallet-cli --restore-deterministic-wallet`
2. Enter a name for the restored wallet
3. Enter your 25-word mnemonic seed when prompted
4. Set a restore height (if known) to speed up synchronization
5. Set a strong password for your wallet

## 4. Synchronization

### Daemon Synchronization

* Using remote node: `salvium-wallet-cli --daemon-address node.salvium.io:12345`
* Using local node: Ensure salviumd is running first, then start the wallet

### Wallet Synchronization

After daemon sync, your wallet will scan the blockchain for your transactions. This can take some time, especially for older wallets. You'll see the progress in the terminal.

## 5. Basic Commands

Here are some essential commands for navigating the CLI wallet:

* `help` - Display all available commands
* `balance` - Show your current balance
* `address` - Display your primary Salvium address
* `seed` - Display your mnemonic seed (requires password)
* `version` - Show wallet version
* `exit` - Safely close the wallet

## 6. Sending Salvium

1. Use the `transfer` command:
   ```
   transfer ADDRESS AMOUNT PAYMENT_ID
   ```
2. Replace ADDRESS with the recipient's Salvium address
3. Replace AMOUNT with the amount to send
4. PAYMENT_ID is optional
5. Confirm the transaction details and enter your password

### Additional Transfer Options

* `transfer_new` - Similar to transfer but creates a new transaction
* `sweep_all` - Send all unlocked balance to an address
* `sweep_below` - Send amounts below a threshold to an address

## 7. Receiving Salvium

1. Use the `address` command to display your primary address
2. Use `address new LABEL` to create a new subaddress with an optional label
3. Use `address all` to view all your addresses

## 8. Transaction Management

* `show_transfers` - Display all incoming and outgoing transactions
* `show_transfers in` - Show only incoming transactions
* `show_transfers out` - Show only outgoing transactions
* `show_transfers pending` - Show pending transactions
* `show_transfers failed` - Show failed transactions

## 9. Mining

Salvium supports solo mining directly from the wallet:

1. Use the `start_mining THREADS` command (replace THREADS with the number of CPU threads to use)
2. Use `stop_mining` to stop mining

Note: Solo mining with a regular computer is unlikely to find blocks. Consider joining a mining pool for more consistent rewards.

## 10. Staking

Salvium introduces staking capabilities:

1. Use the `stake AMOUNT` command (replace AMOUNT with the number of SAL to stake)
2. View staking transactions with `show_transfers`
3. Check current staking with `staking_status`

## 11. Advanced Features

### Exchange Mode

For users who need to manage multiple accounts, the wallet offers exchange mode:
1. Start wallet with `salvium-wallet-cli --exchange-mode`
2. Use `account` commands to manage multiple accounts
3. For more information, see [Exchange Mode in Salvium CLI Wallet](https://salvium.io/salvium-knowledge-base/exchange-mode-in-salvium-cli-wallet/)

### Wallet RPC Interface

For developers who want to integrate with Salvium:
1. `salvium-wallet-rpc` offers a JSON-RPC interface
2. Start with proper security settings
3. See the full API documentation for details

## 12. Security and Privacy

* Always keep your seed phrase safe and offline
* Use a strong, unique password for your wallet
* Consider using a hardware wallet for large amounts
* Use subaddresses to enhance privacy
* Run a full node if possible for maximum privacy

## 13. Troubleshooting

* Wallet not syncing: Check your internet connection and node settings
* Incorrect balance: Try rescanning the blockchain with `rescan_bc`
* Transaction stuck: Wait for more confirmations or try `rescan_bc`
* Connection issues: Ensure no other daemons are running, and that your firewall allows connections

## 14. Glossary

* Salvium (SAL): The native cryptocurrency of the Salvium network
* Mnemonic Seed: 25-word phrase used to recover your wallet
* Subaddress: Unique addresses generated from your main address for enhanced privacy
* Stake: Locking up SAL to support network operations and earn rewards
* DeFi: Decentralized Finance features built into Salvium

Remember, this guide is a living document and will be updated as new features are added to Salvium. Always refer to the official Salvium website and documentation for the most up-to-date information.