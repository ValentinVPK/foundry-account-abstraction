# Account Abstraction Implementation

This project demonstrates account abstraction implementations for both Ethereum (ERC-4337) and zkSync Era, showcasing how smart contract wallets can replace traditional EOAs (Externally Owned Accounts) to provide enhanced flexibility and security.

## Project Overview

1. Implementation of ERC-4337 account abstraction on Ethereum
2. Implementation of native account abstraction on zkSync Era
3. Testing and deployment of both implementations

## What is Account Abstraction?

Account abstraction is a paradigm shift in how blockchain accounts operate, allowing for programmable accounts with enhanced security features, customized authorization logic, improved user experience, and more flexible transaction handling.

### Traditional Accounts vs. Account Abstraction

In traditional blockchain systems:

- EOAs (Externally Owned Accounts) are controlled by private keys
- Smart contracts have addresses but cannot initiate transactions
- Fixed gas payment mechanisms
- Limited recovery options when keys are lost

Account abstraction bridges this gap by:

- Making accounts fully programmable with customizable validation logic
- Allowing batch transactions, sponsored transactions, and complex authorization schemes
- Enabling social recovery, session keys, and other security enhancements
- Supporting subscription payments and custom fee mechanisms

## ERC-4337 Account Abstraction (Ethereum)

ERC-4337 is an Ethereum standard for account abstraction that achieves most of the benefits without requiring consensus layer changes.

### Key Components

1. **UserOperation**: A data structure representing an operation to be executed, including:

   - `sender`: The account contract address executing the operation
   - `nonce`: Prevents replay attacks
   - `initCode`: Code for deploying the account contract (if not deployed)
   - `callData`: The call data to execute
   - `signature`: The account-specific signature validating the operation

2. **EntryPoint Contract**: The central contract that:

   - Receives UserOperations
   - Validates operations with the account contracts
   - Executes the operations if validated

3. **Bundlers**: Specialized entities that:

   - Collect UserOperations from users
   - Bundle them together
   - Submit them to the EntryPoint contract

4. **Smart Contract Wallets**: Our implementation (`MinimalAccount`) that:
   - Implements the IAccount interface
   - Validates signatures and permissions
   - Executes transactions when called by the EntryPoint

### MinimalAccount Implementation

Our `MinimalAccount` contract:

- Implements the ERC-4337 `IAccount` interface
- Uses ECDSA signatures for transaction validation
- Supports ownership transfer (from OpenZeppelin's Ownable)
- Validates operations through the `validateUserOp` function
- Allows execution from both the EntryPoint and the owner
- Handles prefunding for gas costs

### The ERC-4337 Flow

1. User signs a UserOperation with their wallet
2. UserOperation is submitted to a bundler
3. Bundler calls the EntryPoint contract
4. EntryPoint validates the operation with the account contract
5. If valid, EntryPoint executes the operation

## zkSync Era Account Abstraction

zkSync Era offers native account abstraction as a core feature of the protocol.

### Key Differences from ERC-4337

1. **Native Protocol Support**: zkSync natively supports account abstraction with transaction type 113
2. **System Contracts**: Built-in system contracts handle validation and execution
3. **Simplified Flow**: No need for bundlers or EntryPoint contracts
4. **Lower Gas Costs**: More efficient with lower overhead
5. **Bootloader**: Replaces the EntryPoint role in transaction validation

### ZkMinimalAccount Implementation

Our `ZkMinimalAccount` contract:

- Implements zkSync's `IAccount` interface
- Uses the BOOTLOADER_FORMAL_ADDRESS for validation
- Handles verification, nonce management, and fee payment
- Provides both internal and external execution paths
- Customizes signature validation using ECDSA
- Interfaces with zkSync system contracts

### The zkSync Account Abstraction Flow

1. User creates a transaction
2. Transaction is signed with the account owner's key
3. Transaction is submitted to zkSync with type 113
4. Bootloader calls the account's `validateTransaction` function
5. Account validates the signature and checks the nonce
6. If valid, the `executeTransaction` function is called

## Implementation Details

### Ethereum MinimalAccount

```solidity
// Key functions
function validateUserOp(PackedUserOperation calldata userOp, bytes32 userOpHash, uint256 missingAccountFunds)
    external returns (uint256 validationData);

function execute(address dest, uint256 value, bytes calldata functionData)
    external;
```

### zkSync ZkMinimalAccount

```solidity
// Key functions
function validateTransaction(bytes32, bytes32, Transaction memory _transaction)
    external payable returns (bytes4 magic);

function executeTransaction(bytes32, bytes32, Transaction memory _transaction)
    external payable;
```

## Testing

The project includes comprehensive test coverage for both implementations:

- Signature validation
- Transaction execution
- Error handling
- Gas consumption
- Entry point interaction
- Security checks

## Development and Deployment

This project uses Foundry for development, testing, and deployment:

```bash
# Install dependencies
forge install

# Run tests
forge test

# Deploy contracts
forge script DeployMinimal
```

## Advanced Features & Extensions

Future extensions could include:

- Multi-signature validation
- Time-locked transactions
- Gas sponsoring mechanisms
- Account recovery features
- Batch transaction execution
- On-chain reputation systems

## Security Considerations

Account abstraction introduces new security models:

- Signature schemes must be carefully validated
- Nonce management prevents replay attacks
- Access control requires careful implementation
- Gas estimation needs special handling
- Reentrancy protections are essential

## Resources and References

- [ERC-4337 Specification](https://eips.ethereum.org/EIPS/eip-4337)
- [zkSync Account Abstraction Documentation](https://docs.zksync.io/build/developer-reference/account-abstraction.html)
- [Foundry Documentation](https://book.getfoundry.sh/)

## Contributors

This project was developed to demonstrate account abstraction techniques on multiple platforms.
