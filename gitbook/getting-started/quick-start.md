# Quick Start Guide

Welcome to FunPump! This guide will help you get started with launching your first token using our platform.

## Prerequisites

- Solana wallet (Phantom, Solflare, etc.)
- SOL for transaction fees
- Modern web browser with microphone support (for voice features)

## Installation

1. Visit [FunPump Platform](https://funpump.io)
2. Connect your Solana wallet
3. Grant microphone permissions (optional, for voice features)

## Your First Token Launch

### Option 1: Virtual Environment (Recommended)

1. Click "Create Virtual Environment"
2. Choose environment name
3. Set initial parameters:
   - Token name
   - Symbol
   - Initial price
   - Liquidity amount

4. Test your token:
   - Execute test trades
   - Monitor price impact
   - Analyze market behavior
   - Chat with other users

5. When ready, convert to real token:
   - Choose standard or compressed format
   - Confirm parameters
   - Execute launch transaction

### Option 2: Voice Launch

1. Click the microphone icon
2. Say "Launch a new token"
3. Follow the voice assistant's prompts:
   ```
   Assistant: "What would you like to name your token?"
   You: "Moon Rocket"
   Assistant: "What symbol should we use?"
   You: "MOON"
   ```

4. Confirm details verbally
5. Execute launch

### Option 3: Direct Launch

1. Click "Launch Token"
2. Fill in token details:
   - Name
   - Symbol
   - Initial price
   - Supply
   - Compression (optional)

3. Review parameters
4. Confirm transaction

## Testing Your Launch

We provide three testing options:

### 1. Simple Mock Test
```typescript
// No real transactions, perfect for development
const mockTest = async () => {
  const launcher = new MockTokenLauncher();
  await launcher.testLaunch({
    name: "Test Token",
    symbol: "TEST"
  });
};
```

### 2. Devnet Test
```typescript
// Real transactions on Solana devnet
const devnetTest = async () => {
  const launcher = new TokenLauncher('devnet');
  await launcher.launchToken({
    name: "Test Token",
    symbol: "TEST"
  });
};
```

### 3. Mainnet Launch
```typescript
// Production launch on Solana mainnet
const mainnetLaunch = async () => {
  const launcher = new TokenLauncher('mainnet-beta');
  await launcher.launchToken({
    name: "Real Token",
    symbol: "REAL"
  });
};
```

## Earning Rewards

Track your progress in the reward system:

1. View your current tier
2. Monitor launch count
3. Track trading volume
4. Claim rewards when available

## Next Steps

- Explore [Virtual Environments](../features/virtual-environments.md)
- Learn about [Voice Commands](../features/voice-commands.md)
- Understand [Compressed Tokens](../features/compressed-tokens.md)
- Check out the [Reward System](../features/reward-system.md)

## Common Questions

### Q: How long does a token launch take?
A: Virtual environment launches are instant. Real token launches typically complete within 1-2 Solana blocks (about 1 second).

### Q: What are the costs?
A: 
- Virtual testing: Free
- Standard launch: ~0.05 SOL
- Compressed launch: ~0.01 SOL

### Q: Can I modify my token after launch?
A: Some parameters can be updated if you maintain authority. Test changes in a virtual environment first.

### Q: How do rewards work?
A: You earn a percentage of fees based on your tier level, from 5% to 25%.

## Support

- Join our [Discord](https://discord.gg/funpump)
- Visit the [Help Center](https://help.funpump.io)
- Email: support@funpump.io

## Security Tips

1. Always test in virtual environment first
2. Verify transaction details before signing
3. Keep your wallet secure
4. Start with small amounts
5. Monitor your authority keys
