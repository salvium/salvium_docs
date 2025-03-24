# About Privacy

Salvium adopts a robust set of privacy features based on the Cryptonote protocol, similar to the standards set by Monero, ensuring comprehensive privacy for all transactions and account balances. This includes protecting the identity of the sender, the receiver, and the transaction amount.

Salvium utilizes advanced privacy-enhancing technologies from the Cryptonote code base to secure your funds while adhering to compliance requirements. These features include:

* Ring Signatures: These help obscure the identities of the sender in a transaction, enhancing privacy.
* Ring Confidential Transactions (RingCT): This feature conceals the amount of each transaction, ensuring the details of financial transfers remain private.
* Stealth Addresses: Utilized to protect the recipient's identity by creating a one-time address for each transaction.

Please be aware that staking and DeFi features could potentially compromise privacy. Please read the relevant documentation to ensure that you are aware of the privacy implications for each type of transaction.

## Why is privacy important for Salvium?

Salvium is built on a foundation of robust privacy, leveraging and enhancing the privacy features of the CryptoNote protocol. This page outlines the key privacy technologies employed by Salvium and their implications for users.

## Core Privacy Features

Salvium incorporates advanced privacy-enhancing technologies from the CryptoNote codebase, ensuring comprehensive protection for all transactions and account balances. These features work in concert to safeguard the identity of senders and receivers, as well as the amounts transferred.

### Ring Signatures

**Function**: Ring signatures help obscure the identity of the sender in a transaction.

**How it works**:
* When a user initiates a transaction, their signature is combined with past transaction outputs on the blockchain.
* This creates a "ring" of possible signers, making it computationally infeasible to determine which member of the group actually signed the transaction.
* The larger the ring size, the greater the privacy provided.

**Benefit**: Enhances transaction privacy by making it difficult to trace the true origin of a transaction.

### Ring Confidential Transactions (RingCT)

**Function**: RingCT conceals the amount of each transaction.

**How it works**:
* Uses cryptographic commitments and range proofs to hide transaction amounts.
* Allows verification that inputs and outputs balance without revealing the actual amounts.

**Benefit**: Ensures the details of financial transfers remain private, preventing outside observers from determining the value of transactions.

### Stealth Addresses

**Function**: Protects the recipient's identity by creating a one-time address for each transaction.

**How it works**:
* Instead of publishing the recipient's actual address on the blockchain, a one-time stealth address is generated for each transaction.
* Only the recipient can detect and spend funds sent to this address using their private view key.

**Benefit**: Prevents linking multiple transactions to the same recipient, enhancing recipient privacy.

## Compliance and Selective Transparency

While Salvium prioritizes privacy, it also incorporates features to enable compliance with regulatory requirements:

* **View Keys (Phase 2)**: Users can optionally share view keys with authorized parties (like exchanges or auditors) to provide transaction history visibility without compromising overall privacy.
* **Refundable Transactions**: This feature allows for the return of unauthorized or incorrect transactions, balancing privacy with practical and regulatory needs.

## Privacy Considerations for Staking and DeFi

Users should be aware that certain features, particularly staking and future DeFi functionalities, may have implications for privacy:

* **Staking**: The act of staking and receiving rewards may create patterns that could potentially be analyzed to infer information about a user's holdings.
* **DeFi Interactions**: Engaging with DeFi protocols may require revealing certain information to interact with smart contracts effectively.

It is crucial for users to read the relevant documentation for each feature to understand its specific privacy implications. The Salvium team is committed to maximizing privacy even in these more complex interactions, but users should make informed decisions based on their privacy needs.