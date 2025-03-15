# Compressed Tokens

FunPump leverages Solana's Light Protocol to offer compressed token launches, providing significant cost savings and improved scalability through state compression technology.

## Overview

Compressed tokens offer several advantages:
- Reduced storage costs
- Lower transaction fees
- Improved scalability
- Enhanced privacy features

## Technical Implementation

### Compressed Token Service
```typescript
interface CompressedTokenConfig {
  name: string;
  symbol: string;
  uri: string;
  maxSupply: bigint;
  decimals: number;
  merkleTree: PublicKey;
}

class CompressedTokenService {
  async createCompressedToken(
    creator: PublicKey,
    config: CompressedTokenConfig,
    initialBuyAmount: bigint
  ): Promise<{ transaction: Transaction; mint: Keypair }> {
    // Implementation details...
  }
}
```

### Merkle Tree Management
```typescript
private async createMerkleTree(): Promise<PublicKey> {
  const maxDepth = 14;    // Supports up to 16384 tokens
  const canopyDepth = 10;
  const space = this.getMerkleTreeBufferSize(maxDepth, canopyDepth);
  
  // Create and initialize merkle tree...
  return merkleTree.publicKey;
}
```

## Launch Process

### 1. Initialize Merkle Tree
- Create account for tree storage
- Set maximum depth and canopy
- Initialize tree structure

### 2. Create Compressed Token
```typescript
const compressedToken = await compressedTokenService.createCompressedToken(
  creator,
  {
    name: "Test Token",
    symbol: "TEST",
    uri: "https://...",
    maxSupply: 1000000n,
    decimals: 9,
    merkleTree: merkleTreePubkey
  },
  initialBuyAmount
);
```

### 3. Initialize Bonding Curve
```typescript
const bondingCurve = await initializeCompressedBondingCurve(
  mint.publicKey,
  creator,
  initialBuyAmount
);
```

### 4. Create Metadata
```typescript
const metadata = await createCompressedMetadata(
  mint.publicKey,
  creator,
  metadataConfig
);
```

## Trading Operations

### Minting Tokens
```typescript
async mintCompressedTokens(
  mint: PublicKey,
  authority: PublicKey,
  recipient: PublicKey,
  amount: bigint
): Promise<Transaction> {
  // Create mint instruction...
  return transaction;
}
```

### Transferring Tokens
```typescript
async transferCompressedTokens(
  mint: PublicKey,
  sender: PublicKey,
  recipient: PublicKey,
  amount: bigint
): Promise<Transaction> {
  // Create transfer instruction...
  return transaction;
}
```

### Checking Balances
```typescript
async getCompressedTokenBalance(
  mint: PublicKey,
  owner: PublicKey
): Promise<bigint> {
  // Query compressed balance account...
  return balance;
}
```

## Virtual Environment Integration

### Testing Compressed Tokens
1. Create virtual environment
2. Simulate compressed token launch
3. Test trading operations
4. Analyze gas savings
5. Convert to real token

### Conversion Process
```typescript
const convertVirtualToCompressed = async (
  virtualEnvId: string,
  tokenConfig: CompressedTokenConfig
): Promise<TransactionSignature> => {
  // Convert virtual token to compressed token...
  return signature;
};
```

## Cost Comparison

### Standard vs Compressed Tokens

| Operation          | Standard Cost | Compressed Cost | Savings |
|-------------------|---------------|-----------------|----------|
| Token Creation    | 0.05 SOL     | 0.01 SOL       | 80%     |
| Transfer          | 0.00001 SOL  | 0.000002 SOL   | 80%     |
| Metadata Update   | 0.00001 SOL  | 0.000002 SOL   | 80%     |

## Best Practices

### For Token Creators
1. Use appropriate tree depth
2. Plan token supply carefully
3. Consider metadata size
4. Test in virtual environment
5. Monitor gas costs

### For Developers
1. Handle merkle proofs
2. Implement proper validation
3. Manage tree updates
4. Monitor tree capacity
5. Handle edge cases

## Security Considerations

### Tree Security
- Proper authority checks
- Canopy validation
- State verification
- Proof validation

### Transaction Safety
- Authority verification
- Amount validation
- Tree capacity checks
- State consistency

## Performance Optimization

### Tree Management
```typescript
private getMerkleTreeBufferSize(
  maxDepth: number,
  canopyDepth: number
): number {
  const nodeSize = 32; // Size of each node in bytes
  const maxNodes = Math.pow(2, maxDepth) - 1;
  const canopyNodes = Math.pow(2, canopyDepth) - 1;
  
  return nodeSize * (maxNodes + canopyNodes);
}
```

### Batch Operations
```typescript
async batchMintCompressedTokens(
  mint: PublicKey,
  recipients: PublicKey[],
  amounts: bigint[]
): Promise<Transaction> {
  // Create batch mint instruction...
  return transaction;
}
```

## Error Handling

### Common Issues
1. Tree capacity exceeded
2. Invalid proofs
3. Authority mismatch
4. State inconsistency

### Resolution Steps
```typescript
try {
  await compressedOperation();
} catch (error) {
  if (error instanceof TreeCapacityError) {
    // Handle capacity error...
  } else if (error instanceof ProofValidationError) {
    // Handle proof error...
  }
  // Handle other errors...
}
```

## Future Enhancements

Planned improvements include:
1. Advanced compression techniques
2. Multi-tree support
3. Automated tree management
4. Enhanced privacy features
5. Cross-chain compatibility
6. Improved gas optimization
7. Advanced metadata compression
8. Batch operation optimization

## Migration Guide

### From Standard to Compressed
1. Create merkle tree
2. Initialize compressed token
3. Migrate balances
4. Update references
5. Verify state

### Example Migration
```typescript
const migrateToCompressed = async (
  standardToken: PublicKey,
  holders: PublicKey[]
): Promise<TransactionSignature> => {
  // Create compressed token
  const compressedToken = await createCompressedToken();
  
  // Migrate balances
  for (const holder of holders) {
    await migrateBalance(holder, standardToken, compressedToken);
  }
  
  return signature;
};
```
