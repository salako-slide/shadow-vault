# ShadowVault

A privacy-preserving protocol for Bitcoin transactions on Stacks L2, enabling confidential deposits and withdrawals using zero-knowledge proofs.

## Overview

ShadowVault is a sophisticated smart contract that implements a privacy layer for Bitcoin transactions on the Stacks L2 network. It leverages Merkle trees and zero-knowledge proofs to enable private transactions while maintaining regulatory compliance.

## Features

- **Private Deposits**: Users can deposit funds without revealing transaction details
- **Confidential Withdrawals**: Secure withdrawals using zero-knowledge proofs
- **SIP-010 Integration**: Full compatibility with standard fungible token interface
- **Merkle Tree Implementation**: 20-level Merkle tree for commitment storage
- **Nullifier Protection**: Prevents double-spending through nullifier tracking

## Technical Architecture

### Core Components

1. **Merkle Tree**

   - Height: 20 levels
   - Maximum capacity: 2^20 deposits
   - Zero value: `0x0000000000000000000000000000000000000000000000000000000000000000`

2. **Data Structures**
   - `deposits`: Maps commitments to leaf indices and timestamps
   - `nullifiers`: Tracks used nullifiers to prevent double-spending
   - `merkle-tree`: Stores the Merkle tree nodes

### Key Functions

#### Public Functions

1. `deposit`

   - Parameters:
     - `commitment`: 32-byte commitment
     - `amount`: Token amount
     - `token`: SIP-010 token contract
   - Functionality:
     - Validates input parameters
     - Transfers tokens to contract
     - Updates Merkle tree
     - Returns leaf index

2. `withdraw`
   - Parameters:
     - `nullifier`: 32-byte nullifier
     - `root`: Merkle root
     - `proof`: Merkle proof (20 elements)
     - `recipient`: Withdrawal recipient
     - `token`: SIP-010 token contract
     - `amount`: Withdrawal amount
   - Functionality:
     - Verifies proof validity
     - Checks nullifier uniqueness
     - Processes token transfer
     - Returns success status

#### Read-Only Functions

- `get-current-root`: Returns current Merkle root
- `is-nullifier-used`: Checks if nullifier has been used
- `get-deposit-info`: Retrieves deposit information

### Error Codes

```clarity
ERR-NOT-AUTHORIZED (u1001)         // Authorization failure
ERR-INVALID-AMOUNT (u1002)         // Invalid transaction amount
ERR-INSUFFICIENT-BALANCE (u1003)    // Insufficient funds
ERR-INVALID-COMMITMENT (u1004)      // Invalid commitment format
ERR-NULLIFIER-ALREADY-EXISTS (u1005)// Double-spend attempt
ERR-INVALID-PROOF (u1006)          // Invalid Merkle proof
ERR-TREE-FULL (u1007)              // Merkle tree capacity reached
```

## Security Considerations

1. **Zero-Knowledge Proofs**

   - Ensures transaction privacy
   - Validates withdrawal claims without revealing details

2. **Nullifier System**

   - Prevents double-spending
   - Maintains withdrawal uniqueness

3. **Input Validation**

   - Comprehensive parameter checking
   - Protection against invalid inputs

4. **Balance Verification**
   - Checks contract balance before withdrawals
   - Validates user balances for deposits

## Integration Guide

### Deposit Flow

1. Generate commitment and nullifier pair
2. Call `deposit` function with:
   ```clarity
   (contract-call? .shadowvault deposit
       commitment
       amount
       token-contract)
   ```
3. Store commitment data securely

### Withdrawal Flow

1. Generate zero-knowledge proof
2. Call `withdraw` function with:
   ```clarity
   (contract-call? .shadowvault withdraw
       nullifier
       root
       proof
       recipient
       token-contract
       amount)
   ```

## Best Practices

1. **Commitment Generation**

   - Use cryptographically secure random values
   - Keep commitment-nullifier pairs private

2. **Proof Generation**

   - Generate proofs client-side
   - Verify all inputs before submission

3. **Error Handling**
   - Always check return values
   - Handle all error cases appropriately

## Limitations

- Fixed Merkle tree height (20 levels)
- Maximum deposit capacity: 1,048,576 (2^20) deposits
- Requires off-chain proof generation
- Token-specific implementation

## Testing

Recommended test scenarios:

1. Basic deposit and withdrawal flows
2. Edge cases:
   - Zero amounts
   - Invalid proofs
   - Tree capacity limits
3. Double-spend attempts
4. Invalid nullifier checks
5. Balance verification

## Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to your branch
5. Create a Pull Request
