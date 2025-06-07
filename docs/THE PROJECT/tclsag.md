# T-CLSAG: Two-scalar CLSAG Signature Scheme

## Overview

T-CLSAG (Two-scalar Concise Linkable Spontaneous Anonymous Group signatures) is Salvium's groundbreaking extension of Monero's proven CLSAG signature scheme. It enables proving knowledge of two scalar secrets while maintaining all the privacy, efficiency, and compatibility guarantees of traditional ring signatures.

## What is T-CLSAG?

T-CLSAG addresses a fundamental challenge in modern privacy-focused blockchain technology: how to support advanced addressing schemes like Carrot addressing while preserving the security and anonymity properties that make ring signatures so powerful.

### The Challenge

Traditional CLSAG signatures work with public keys of the form:
```
K = xG
```
Where `x` is a secret scalar and `G` is an elliptic curve generator.

Carrot addressing introduces a more sophisticated format:
```
Ko = xG + yT
```
Where both `x` and `y` are secret scalars, and `G` and `T` are distinct elliptic curve generators.

### The Solution

T-CLSAG extends the CLSAG framework to prove knowledge of both `x` and `y` without compromising:
- **Anonymity**: The real signer remains hidden within the ring
- **Linkability**: Double-spend protection through key images
- **Conciseness**: Signature size remains practical
- **Backward Compatibility**: Existing infrastructure continues to work

## Why T-CLSAG Matters

### Enhanced Privacy Features

T-CLSAG enables several advanced privacy capabilities:

#### Forward Secrecy
Protection against future cryptographic advances, including potential quantum computing threats. Even if encryption is broken in the future, past transaction details remain confidential.

#### Regulatory Compliance
Enhanced cryptographic flexibility enables selective transparency features while maintaining strong privacy by default. Users can control their disclosure level as needed.

#### Advanced DeFi Applications
The dual-secret framework provides the foundation for sophisticated decentralized finance applications that maintain privacy throughout complex smart contract interactions.

### Technical Advantages

1. **Dual-Secret Proofs**: Prove knowledge of two secrets without revealing either
2. **Ring Signature Preservation**: All anonymity properties of traditional ring signatures remain intact
3. **Efficient Verification**: Minimal computational overhead compared to standard CLSAG
4. **Scalable Design**: Works efficiently with multiple inputs and varying ring sizes

## How T-CLSAG Works

### Key Components

#### 1. Key Image Computation
For backward compatibility, key images maintain the traditional structure:
```
Lj = xj · Hp(Kπ,j)
```
Where `Hp` is a hash-to-point function and `π` denotes the real signer's position.

#### 2. Aggregate Public Keys
T-CLSAG introduces aggregate public keys that combine multiple inputs:
```
Wi = Σ Hn(Tj, R, L1, ..., Lm) · Ki,j
```
This aggregation allows efficient proof across multiple inputs while maintaining anonymity.

#### 3. Extended Challenge Chain
The signature verification uses an iterative challenge computation:
```
ci+1 = Hn(Tc, R, m, [rx,iG + ry,iT + ciWi], [rx,iHp(Ki) + ciW̃])
```
This ensures both scalar secrets must be known to complete the proof.

#### 4. Domain Separation
Careful use of domain separators prevents cryptographic mixing:
- `Tj`: Input-specific separators ensuring unique contribution per input
- `Tc`: Challenge-specific separators distinguishing hash contexts

### Signature Process

#### For the Real Signer (position π):

1. **Generate Blinding Factors**:
   ```
   α, β ∈R Zl
   ```

2. **Compute Aggregate Secrets**:
   ```
   wx,π = Hn(T1, R, L1, ..., Lm) · xπ
   wy,π = Hn(T1, R, L1, ..., Lm) · yπ
   ```

3. **Generate Initial Challenge**:
   ```
   cπ+1 = Hn(Tc, R, m, [αG + βT], [αHp(Kπ)])
   ```

4. **Compute Real Responses**:
   ```
   rx,π = α - cπwx,π mod l
   ry,π = β - cπwy,π mod l
   ```

#### For Other Ring Members:
Random values `rx,i, ry,i` are used to maintain indistinguishability.

### Verification Process

1. **Recompute Aggregates**: Calculate `Wi` and `W̃` for all ring members
2. **Initialize Challenge Chain**: Set `c1` based on message and ring
3. **Iterate Through Ring**: Compute challenge chain for each position
4. **Check Closure**: Verify `cn+1 = c1`
5. **Validate Key Images**: Ensure no double-spending

## Security Properties

### Anonymity
All non-signers' responses are cryptographically random, making them indistinguishable from the real signer's response. Without knowledge of the signing position π, no party can be identified as the actual signer.

### Linkability
Key images preserve the traditional structure, ensuring that spending the same output twice will produce identical key images, enabling double-spend detection.

### Zero-Knowledge
The protocol reveals nothing about the secret values `x` or `y` beyond what is necessary for signature verification. Blinding values prevent any leakage of private keys.

### Completeness
When correct secret keys are used, the signature verification will always succeed due to the mathematical relationship between the commitment and response values.

## Implementation Considerations

### Computational Requirements
- **Signing**: Additional computation due to dual-secret handling and aggregation
- **Verification**: Minimal overhead compared to standard CLSAG
- **Storage**: Signature size scales linearly with inputs and ring size

### Compatibility Features
- **Key Image Structure**: Unchanged from traditional CLSAG
- **Ring Format**: Compatible with existing ring construction
- **Challenge System**: Natural extension of proven CLSAG framework

### Optimization Opportunities
- **Precomputation**: Aggregation hashes can be computed during wallet initialization
- **Batch Verification**: Multiple signatures can be verified together
- **Caching**: Common computations can be stored for reuse

## Applications and Use Cases

### Carrot Addressing Support
T-CLSAG is specifically designed to enable Carrot addressing (`Ko = xG + yT`), unlocking:
- Enhanced privacy features
- Forward secrecy properties
- Regulatory compliance capabilities
- Advanced cryptographic flexibility

### Private DeFi Integration
The dual-secret framework enables:
- **Confidential Smart Contracts**: Privacy-preserving automated agreements
- **Private Token Swaps**: Anonymous decentralized exchange functionality  
- **Compliance-Ready Privacy**: Selective disclosure for regulatory requirements
- **Advanced Staking**: Private yield generation with public verification

### Future-Proofing
T-CLSAG provides the cryptographic foundation for:
- Post-quantum privacy protection
- Advanced addressing schemes
- Enhanced compliance features
- Next-generation DeFi applications

## Technical Specifications

### Cryptographic Primitives
- **Elliptic Curve**: Prime-order subgroup with generators G and T
- **Hash Functions**: Cryptographically secure functions `Hn` and `Hp`
- **Scalar Field**: Integers modulo curve order `l`

### Domain Separators
- **Tj**: Input-specific uniqueness identifiers
- **Tc**: Challenge computation context separators
- **Message m**: Transaction data being signed

### Signature Components
A complete T-CLSAG signature includes:
- Message `m`
- Ring of public keys `R = {Ki,j}`
- Key images `L1, ..., Lm`
- Signature `(c1, {(rx,i, ry,i)})`

## Comparison with Other Schemes

### vs. Standard CLSAG
| Feature | CLSAG | T-CLSAG |
|---------|-------|---------|
| Secret Keys | Single (x) | Dual (x, y) |
| Key Format | K = xG | Ko = xG + yT |
| Forward Secrecy | Limited | Enhanced |
| Compliance Features | Basic | Advanced |
| Signature Size | Baseline | Minimal increase |

### vs. Other Multi-Secret Schemes
T-CLSAG offers unique advantages:
- **Efficiency**: Minimal computational overhead
- **Compatibility**: Works with existing infrastructure
- **Security**: Proven mathematical foundations
- **Flexibility**: Supports various addressing schemes

## Development Status

### Current State
- **Mathematical Framework**: Complete and submitted for audit
- **Implementation**: Core development finished
- **Testing**: Comprehensive validation completed
- **Audit**: Under review by Cypher Stack

### Next Steps
1. **Cryptographic Audit**: Formal security validation
2. **Integration Testing**: Mainnet preparation
3. **Performance Optimization**: Final efficiency improvements
4. **Community Review**: Public feedback incorporation

## Conclusion

T-CLSAG represents a significant advancement in privacy-preserving signature schemes, enabling the next generation of blockchain privacy features while maintaining the proven security properties of ring signatures. By supporting dual-secret proofs with minimal overhead, T-CLSAG provides the foundation for advanced addressing schemes, regulatory compliance features, and sophisticated DeFi applications.

The successful development and audit of T-CLSAG positions Salvium at the forefront of privacy-focused blockchain technology, delivering practical solutions to theoretical challenges that have long constrained the development of truly private and compliant decentralized systems.

---

## Further Reading

- [CLSAG: Concise Linkable Ring Signatures](https://eprint.iacr.org/2019/654.pdf)
- [Carrot Addressing Specification](https://github.com/jeffro256/carrot/blob/master/carrot.md)
- [Salvium Technical Documentation](https://salvium.io/docs)
- [Ring Signatures in Cryptocurrency](https://web.getmonero.org/resources/research-lab/)

## Glossary

**CLSAG**: Concise Linkable Spontaneous Anonymous Group signatures - Monero's current signature scheme

**Key Image**: A cryptographic construction that enables double-spend detection while preserving privacy

**Ring Signature**: A cryptographic signature that proves the signer belongs to a group without revealing which member signed

**Forward Secrecy**: Protection against future cryptographic advances that might compromise current encryption

**Domain Separation**: Cryptographic technique to prevent unintended interactions between different protocol components
