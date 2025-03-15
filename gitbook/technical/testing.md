# Testing Guide

FunPump implements a comprehensive testing strategy with three distinct testing environments to ensure reliability and security of token launches.

## Testing Environments

### 1. Simple Mock Test
The quickest way to verify functionality during development:

```typescript
// test-simple.ts
describe('Token Launch Tests', () => {
  it('should simulate token launch without blockchain', async () => {
    const mockLauncher = new MockTokenLauncher();
    const result = await mockLauncher.createToken({
      name: "Test Token",
      symbol: "TEST",
      initialPrice: 0.1
    });
    expect(result.success).toBe(true);
  });
});
```

Key features:
- No blockchain connection required
- Rapid development testing
- In-memory state simulation
- Quick feedback loop

### 2. Devnet Testing
Test with actual blockchain transactions on Solana's devnet:

```typescript
// test-launch.ts
describe('Devnet Launch Tests', () => {
  let connection: Connection;
  let payer: Keypair;

  beforeEach(async () => {
    connection = new Connection(clusterApiUrl('devnet'));
    payer = Keypair.generate(); // Random keypair for testing
    
    // Airdrop SOL for testing
    const signature = await connection.requestAirdrop(
      payer.publicKey,
      LAMPORTS_PER_SOL
    );
    await connection.confirmTransaction(signature);
  });

  it('should launch token on devnet', async () => {
    const launcher = new TokenLauncher(connection, payer);
    const result = await launcher.launchToken({
      name: "Test Token",
      symbol: "TEST",
      uri: "https://test.uri",
      decimals: 9
    });
    expect(result.mint).toBeDefined();
  });
});
```

Benefits:
- Real transaction testing
- Network interaction verification
- Gas cost estimation
- Error handling validation

### 3. Production Testing
Final verification on mainnet-beta:

```typescript
// test-production.ts
describe('Production Launch Tests', () => {
  const MAINNET_RPC = process.env.MAINNET_RPC;
  
  beforeEach(() => {
    // Ensure proper environment setup
    expect(MAINNET_RPC).toBeDefined();
  });

  it('should verify mainnet deployment', async () => {
    const launcher = new TokenLauncher(
      new Connection(MAINNET_RPC!),
      loadKeypair() // Load from secure source
    );
    
    // Production verification...
  });
});
```

Important considerations:
- Real value at stake
- Production RPC endpoints
- Secure key management
- Performance monitoring

## Test Utilities

### Mock WebSocket Service
```typescript
class MockWebSocketService {
  private listeners: Map<string, Function[]> = new Map();

  connect() {
    return Promise.resolve();
  }

  subscribe(event: string, callback: Function) {
    if (!this.listeners.has(event)) {
      this.listeners.set(event, []);
    }
    this.listeners.get(event)!.push(callback);
  }

  emit(event: string, data: any) {
    const callbacks = this.listeners.get(event) || [];
    callbacks.forEach(callback => callback(data));
  }
}
```

### Virtual Environment Testing
```typescript
describe('Virtual Environment Tests', () => {
  let virtualEnv: VirtualEnvironment;
  let mockWs: MockWebSocketService;

  beforeEach(() => {
    mockWs = new MockWebSocketService();
    virtualEnv = new VirtualEnvironment(mockWs);
  });

  it('should simulate trading', async () => {
    const trade = {
      amount: 100n,
      price: 0.1,
      isBuy: true
    };

    await virtualEnv.executeTrade(trade);
    // Verify state updates...
  });
});
```

### Reward System Testing
```typescript
describe('Reward System Tests', () => {
  let rewardSystem: RewardSystem;

  beforeEach(() => {
    rewardSystem = new RewardSystem({
      tiers: [
        { level: 'BRONZE', minLaunches: 0, reward: 0.05 },
        { level: 'SILVER', minLaunches: 5, reward: 0.10 },
        // ... more tiers
      ]
    });
  });

  it('should calculate rewards correctly', () => {
    const reward = rewardSystem.calculateReward({
      launches: 6,
      volume: 1000n,
      currentTier: 'SILVER'
    });
    expect(reward).toBe(100n); // 10% of 1000
  });
});
```

## Testing Voice Commands

### Mock Voice Assistant
```typescript
class MockVoiceAssistant {
  async processCommand(command: string): Promise<{
    intent: string;
    parameters: Record<string, any>;
  }> {
    // Simulate NLP processing...
    return {
      intent: 'LAUNCH_TOKEN',
      parameters: {
        name: 'Test Token',
        symbol: 'TEST'
      }
    };
  }
}

describe('Voice Command Tests', () => {
  let voiceAssistant: MockVoiceAssistant;

  beforeEach(() => {
    voiceAssistant = new MockVoiceAssistant();
  });

  it('should process launch command', async () => {
    const result = await voiceAssistant
      .processCommand('Launch a token called Test Token with symbol TEST');
    expect(result.intent).toBe('LAUNCH_TOKEN');
  });
});
```

## Compressed Token Testing

### Test Helpers
```typescript
const createTestMerkleTree = async (
  connection: Connection,
  payer: Keypair
): Promise<PublicKey> => {
  const treeKeypair = Keypair.generate();
  // Create and initialize test merkle tree...
  return treeKeypair.publicKey;
};

describe('Compressed Token Tests', () => {
  let compressedTokenService: CompressedTokenService;
  let merkleTree: PublicKey;

  beforeEach(async () => {
    merkleTree = await createTestMerkleTree(connection, payer);
    compressedTokenService = new CompressedTokenService(
      connection,
      merkleTree
    );
  });

  it('should create compressed token', async () => {
    const result = await compressedTokenService.createToken({
      name: "Test Compressed",
      symbol: "TESTC",
      maxSupply: 1000000n
    });
    expect(result.mint).toBeDefined();
  });
});
```

## CI/CD Integration

### GitHub Actions Workflow
```yaml
name: FunPump Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      
      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '16'
          
      - name: Install dependencies
        run: npm install
        
      - name: Run simple tests
        run: npm run test:simple
        
      - name: Run devnet tests
        if: github.ref == 'refs/heads/main'
        run: npm run test:devnet
        env:
          DEVNET_RPC: ${{ secrets.DEVNET_RPC }}
```

## Test Coverage

### Jest Configuration
```javascript
// jest.config.js
module.exports = {
  collectCoverageFrom: [
    'src/**/*.{ts,tsx}',
    '!src/**/*.d.ts',
    '!src/**/*.test.{ts,tsx}'
  ],
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    }
  }
};
```

## Best Practices

### 1. Test Organization
- Group related tests
- Use descriptive names
- Maintain test independence
- Clean up after tests

### 2. Error Testing
```typescript
it('should handle invalid parameters', async () => {
  const launcher = new TokenLauncher(connection, payer);
  
  await expect(launcher.launchToken({
    name: "",  // Invalid name
    symbol: "TEST",
    decimals: 9
  })).rejects.toThrow('Invalid token name');
});
```

### 3. State Verification
```typescript
it('should update virtual environment state', async () => {
  const initialState = virtualEnv.getState();
  await virtualEnv.executeTrade(tradeTx);
  const newState = virtualEnv.getState();
  
  expect(newState.reserves).not.toEqual(initialState.reserves);
  expect(newState.price).toBeGreaterThan(initialState.price);
});
```

## Troubleshooting

### Common Issues
1. Network connectivity
2. Invalid keypairs
3. Insufficient balance
4. Rate limiting

### Resolution Steps
```typescript
const retryWithBackoff = async (
  fn: () => Promise<any>,
  maxRetries: number = 3
): Promise<any> => {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fn();
    } catch (error) {
      if (i === maxRetries - 1) throw error;
      await new Promise(r => setTimeout(r, Math.pow(2, i) * 1000));
    }
  }
};
```
