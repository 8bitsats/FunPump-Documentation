# Reward System

FunPump's reward system incentivizes active participation and token launches through a tiered structure that returns a percentage of service fees to users based on their activity level.

## Overview

The reward system is designed to:
- Encourage quality token launches
- Reward active participants
- Create a sustainable ecosystem
- Drive platform adoption

## Tier Structure

### Bronze Tier
- **Requirements**: 0 launches
- **Reward Rate**: 5%
- **Benefits**:
  - Basic reward earnings
  - Virtual environment access
  - Standard support

### Silver Tier
- **Requirements**: 5 launches
- **Reward Rate**: 10%
- **Benefits**:
  - Increased reward rate
  - Priority support
  - Extended virtual testing
  - Basic analytics

### Gold Tier
- **Requirements**: 15 launches
- **Reward Rate**: 15%
- **Benefits**:
  - Premium reward rate
  - Advanced analytics
  - Reduced fees
  - Custom environments

### Platinum Tier
- **Requirements**: 30 launches
- **Reward Rate**: 20%
- **Benefits**:
  - Elite reward rate
  - Custom branding
  - VIP support
  - Market insights

### Diamond Tier
- **Requirements**: 50 launches
- **Reward Rate**: 25%
- **Benefits**:
  - Maximum rewards
  - All premium features
  - Early access
  - Exclusive events

## Reward Calculation

Rewards are calculated based on:

```typescript
interface RewardCalculation {
  baseFee: number;         // Base service fee
  tierMultiplier: number;  // Based on user's tier
  activityBonus: number;   // Additional rewards for consistent activity
  volumeBonus: number;     // Based on trading volume
}

const rewardAmount = baseFee * (tierMultiplier + activityBonus + volumeBonus);
```

## Activity Tracking

The system tracks various activities:

1. **Token Launches**
   - Successful deployments
   - Initial liquidity added
   - Trading volume generated

2. **Trading Activity**
   - Buy/sell transactions
   - Liquidity provision
   - Market making

3. **Community Engagement**
   - Virtual environment participation
   - Chat activity
   - Voice interactions

## Claiming Rewards

### Process
1. Accumulate rewards through activity
2. Check available balance
3. Initiate claim transaction
4. Receive rewards in wallet

### Example Code
```typescript
const claimRewards = async () => {
  const rewards = await rewardSystem.getUnclaimedRewards(userAddress);
  if (rewards > 0) {
    const tx = await rewardSystem.createClaimTransaction(userAddress);
    const signature = await sendAndConfirmTransaction(connection, tx, [payer]);
    return signature;
  }
};
```

## Implementation Details

### Reward Distribution
```typescript
class RewardSystem {
  async distributeRewards(
    user: PublicKey,
    amount: bigint,
    activity: ActivityType
  ): Promise<TransactionSignature> {
    const tier = await this.getUserTier(user);
    const multiplier = this.getTierMultiplier(tier);
    const rewardAmount = amount * multiplier;
    
    return this.sendRewards(user, rewardAmount);
  }
}
```

### Tier Management
```typescript
interface TierRequirements {
  minLaunches: number;
  minVolume: bigint;
  timeFrame: number;
}

class TierManager {
  async updateUserTier(user: PublicKey): Promise<Tier> {
    const stats = await this.getUserStats(user);
    const newTier = this.calculateTier(stats);
    await this.updateTierAccount(user, newTier);
    return newTier;
  }
}
```

## User Interface

The reward system is visualized through the `RewardTierDisplay` component:

```typescript
interface RewardTierDisplayProps {
  totalLaunches: number;
  totalVolume: bigint;
  currentTier: number;
  unclaimedRewards: bigint;
  onClaimRewards: () => Promise<void>;
}
```

Features include:
- Progress visualization
- Tier benefits display
- Reward claiming interface
- Activity history

## Best Practices

### For Users
1. Maintain consistent activity
2. Complete token launches
3. Provide liquidity
4. Engage with community

### For Developers
1. Regular reward distribution
2. Accurate tracking
3. Clear documentation
4. User feedback

## Security Measures

### Transaction Security
- Multi-signature support
- Rate limiting
- Amount validation
- Replay protection

### Account Security
- PDA derivation
- Authority checks
- State validation
- Balance verification

## Future Enhancements

Planned improvements include:
1. Additional tier levels
2. NFT rewards
3. Governance integration
4. Cross-chain rewards
5. Staking mechanisms
