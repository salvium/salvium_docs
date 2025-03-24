# Exchange Mode in Salvium CLI Wallet

Exchange Mode is a specialized set of features in the Salvium CLI Wallet designed to help exchanges comply with MiCA (Markets in Crypto-Assets) regulations. This mode ensures that received payments can be securely and accurately returned if necessary, maintaining regulatory compliance while preserving Salvium's privacy features.

## Key Components of Exchange Mode

Exchange Mode operates in two main parts:

1. **Freezing Incoming Payments:** This feature prevents received funds from being used in other transactions or fees, ensuring they can be returned in their entirety if needed.
2. **Return Payment Functionality:** Allows for the precise return of received payments to their original sender.

### Freezing Incoming Payments

#### Purpose:
* Prevents received payments from being inadvertently used in other transactions or fees.
* Guarantees that the exact amount received can be returned if necessary.

#### Commands:
1. To enable freezing of incoming payments: `set freeze-incoming-payments 1`
2. To disable freezing of incoming payments: `set freeze-incoming-payments 0`

#### Effects:
* When enabled, this feature ensures that specific outputs received remain available for return.
* It's recommended to keep this feature enabled for exchanges to maintain compliance with regulations.

### Processing a Return Payment

To return a payment to its original sender:

**Command:** `return_payment <tx ID>`

Replace `<tx ID>` with the transaction ID of the payment you wish to return.

#### Function:
* This command facilitates the exact return of the received amount.
* It maintains compliance with regulatory requirements by ensuring the precise funds are returned.

## Important Considerations

1. **Regulatory Compliance:**
   * Exchange Mode is designed specifically to support MiCA regulations.
   * It allows exchanges to operate with Salvium while adhering to legal requirements.

2. **Exact Fund Return:**
   * The combination of freezing incoming payments and the return_payment function ensures that the exact received funds can be returned.
   * This precision is crucial for regulatory compliance and accurate record-keeping.

3. **Operational Impact:**
   * Exchanges should be aware that enabling freeze-incoming-payments may affect how received funds can be used or moved within the wallet.
   * It's important to balance compliance needs with operational efficiency.

## Best Practices for Exchanges

1. **Enable Freeze-Incoming-Payments:**
   * Keep this feature enabled by default to ensure compliance readiness.

2. **Regular Audits:**
   * Periodically review frozen payments to ensure they align with your records.

3. **Prompt Processing:**
   * Handle return requests promptly to maintain good customer relations and regulatory compliance.

4. **Documentation:**
   * Maintain clear records of all return transactions for audit purposes.

5. **Staff Training:**
   * Ensure that relevant staff are trained in using Exchange Mode features correctly.

## Conclusion

Exchange Mode in the Salvium CLI Wallet is a crucial feature for exchanges and services that need to comply with MiCA regulations while operating with Salvium. By providing the ability to freeze incoming payments and process exact returns, it bridges the gap between Salvium's privacy-focused design and regulatory requirements. Users, especially exchanges, should familiarize themselves with these features to ensure smooth operations and compliance in their Salvium transactions.