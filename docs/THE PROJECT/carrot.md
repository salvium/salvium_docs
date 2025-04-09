# CARROT: Cryptonote Address on Rerandomizable-RingCT-Output Transactions

## Overview

CARROT is an addressing protocol designed to enhance privacy and usability in Cryptonote-based cryptocurrencies. The acronym stands for Cryptonote Address on Rerandomizable-RingCT-Output Transactions. Originally proposed for Monero, CARROT introduces significant improvements to address structure, wallet functionality, and transaction privacy that Salvium leverages as a foundation for its SPARC protocol.

## Key Features

### Full View-Only Wallets

- Traditional Limitation: In standard Cryptonote systems, view-only wallets can only detect incoming transactions, resulting in inaccurate balance displays
- CARROT Solution: Enables view-only wallets to monitor both incoming and outgoing transactions without compromising security
- Implementation: Uses a view-balance key that can track all wallet activity without the ability to spend

### Forward Secrecy

- Privacy Protection: Ensures that even if encryption is broken in the future, past transaction details remain confidential
- Key Rotation: Implements key rotation mechanisms to limit the impact of key compromise
- Long-term Security: Provides protection for historical transaction data against future cryptographic advances

### Enhanced Security Against Known Attacks

CARROT mitigates specific vulnerabilities in traditional Cryptonote systems:

- Janus Attack: Prevents attackers from linking multiple addresses to the same owner by enabling recipients to recognize malicious outputs
- Burning Bug: Eliminates a vulnerability where users could inadvertently lose funds when spending from duplicate outputs
- Duplicate Output Issues: Provides mechanisms to safely handle duplicate outputs in the blockchain

### Rerandomizable RingCT Transactions

- Enhanced Anonymity Set: Works with Full-Chain Membership Proofs to allow all transaction outputs on the blockchain to serve as potential decoys
- Improved Privacy: Significantly enhances anonymity compared to traditional ring signatures by enabling larger, more effective anonymity sets
- Unlinkability: Makes transactions more resistant to chain analysis and statistical attacks

## Technical Architecture

### Addressing System

CARROT implements a sophisticated addressing system with:

- Advanced Key Hierarchy: Multiple key types for different functional needs
- Wallet Key Tiers: Separation of keys by capability (viewing, spending, address generation)
- Address Format: Computationally indistinguishable from legacy addresses for privacy and compatibility

### Key Components

- View-Balance Key: Private key that can view all incoming and outgoing funds
- Find-Received Key: Capability to identify incoming transactions
- Generate-Address Key: Ability to create new addresses without full wallet access
- Master Key: Root key that enables all wallet functions including spending

### Wallet Operations

CARROT enables advanced wallet operations:

- Tiered Access Control: Different keys for different levels of wallet access
- Subaddress Management: Improved management of multiple addresses without look-ahead limitations
- Address Recognition: Enhanced methods for identifying owned transactions on the blockchain

## Integration with Salvium

Salvium builds on CARROT by:

1. Implementing Core Addressing: Using the CARROT addressing scheme as a foundation
2. Extending with SPARC: Adding spend proofs and anonymized returns capabilities
3. Optimizing for Compliance: Adapting CARROT to work within regulatory frameworks while maintaining [privacy features](../THE%20PROTOCOL/About%20Privacy.md)

## Quantum Resistance Considerations

CARROT includes design elements with future security in mind:

- Key Derivation: Methods designed with long-term security considerations
- Forward Secrecy: Protection of historical data even if future keys are compromised
- Address Construction: Approaches that aim to maintain privacy over the long term

## Benefits for Users

- Improved Privacy: Enhanced protection against various forms of transaction analysis
- Better User Experience: More accurate balance information in all wallet types
- Future-Proofing: Design considerations for emerging threats
- Seamless Upgrades: Backward compatibility with existing addresses

## Relationship to Monero

CARROT was originally proposed for implementation in Monero but has not yet been integrated into the Monero codebase. Salvium has adapted and implemented this protocol to serve as the foundation for its [privacy](../THE%20PROTOCOL/About%20Privacy.md) and [compliance features](../THE%20PROJECT/Compliance%20Statement.md).

## Technical Documentation

For detailed technical specifications, including cryptographic constructions and security proofs, please refer to the Salvium White Paper in our documentation repository.

## FAQ

Q: Is CARROT backward compatible with existing Salvium addresses?  
A: Yes, CARROT is designed to maintain compatibility with existing Salvium addresses while offering enhanced features for new addresses.

Q: How does CARROT improve on traditional Cryptonote addressing?  
A: CARROT adds full view-only wallet capabilities, forward secrecy, protection against various attacks, and support for rerandomizable transactions.

Q: Does CARROT require changes to the consensus protocol?  
A: CARROT primarily affects address formats and wallet operations rather than consensus rules, making it relatively easy to integrate.

Q: How does CARROT relate to SPARC?  
A: CARROT provides the addressing foundation that SPARC extends with spend proofs and anonymized returns capabilities.

Q: Is CARROT exclusive to Salvium?  
A: No, CARROT is an open protocol originally proposed for Monero. Salvium has implemented it for its specific requirements.
