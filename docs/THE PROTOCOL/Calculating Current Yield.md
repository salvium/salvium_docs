# Calculating Current Yield

!!! warning "Important"
    Current yield is not an indication of future yield.

## Overview

In Salvium's staking system, rewards are calculated and distributed in each block. This means every time a block is mined, a portion of the block reward is allocated to stakers. Specifically, 20% of the total block reward is set aside for stakers, while miners receive the remaining 80%. 

This reward system operates per block, ensuring that stakers receive their share consistently with each new block. In this post, any reference to yield is purely an estimate of the average yield for the specified past period or block.

It's important to note that because the reward is paid out with each block, the actual amount stakers receives can vary depending on:

* The current block reward
* The total value locked (TVL) in staking

As the block reward decreases over time ([with emission](https://salvium.io/salvium-knowledge-base/mining-and-emissions/)), the reward per block for stakers also adjusts accordingly. Understanding this block-based distribution is crucial for accurately estimating potential yields and making informed decisions about staking.

## Example: Total Yield Calculation (Past 21,600 Blocks)

1. **Miners' Reward per block:** 120 SAL (current rate)
2. **Stakers' Reward per block:** 120 / 4 = 30 SAL
3. **Total Yield (for all stakers):** 30 SAL × 21,600 blocks ≈ 648,000 SAL

This means that stakers are currently sharing approximately 648,000 SAL between them every 21,600 blocks (approximately 30 days). This yield is distributed proportionally among all stakers.

To determine the yield per staked SAL:

```
Yield per SAL = 648,000 SAL / TVL
```

Since Total Value Locked (TVL) fluctuates, the actual yield per SAL will vary.

## Example: Estimated Yield per SAL Staked

1. **Total Yield (for stakers):** 648,000 SAL (from above example)
2. **Divide:** By current TVL to get yield per SAL staked
3. **Divide:** By 21,600 (staking period)

For example, with a TVL of 4,800,000 SAL and a yield of 648,000 SAL over the last ~30 days:

```
(648,000 (Total Yield for past 21.6k blocks) / 4,800,000 (TVL)) / 21,600 = 0.00000625 SAL
```

!!! note
    Future yields will differ as TVL for the future is unknown, making precise future yield calculations impossible. This "Yield per SAL staked" figure is shown in the wallet. (In CLI type `yield_info` to access this data)

## Example: Estimate Potential Yield

1. **Yield per SAL staked:** 0.00000625 SAL (available in wallet)
2. **Multiplied:** By 21,600 (staking period)
3. **Multiplied:** By quantity of SALs staked

This allows you to calculate the possible yield achieved, assuming the above values for approximately 30 days:

```
(0.00000625 × 21,600 blocks) × 10,000 (Example Stake) = 1,350 SALs yield (13.5%)
```

!!! note
    Future yields will differ as TVL for the future is unknown, making precise future yield calculations impossible.

## Hypothetical Yield Examples

To illustrate how different TVL levels affect yield percentages, here are some hypothetical scenarios. Using a theoretical yield of 648,000 SAL over 21,600 blocks (approximately 30 days), we calculate the yield for various TVL levels.

Remember, these are just examples; actual yields will depend on real-time network conditions. Percentage yield can be calculated simply by:

```
(Yield per SAL staked × 21,600) / TVL × 100
```

| TVL (SAL) | Stakers Reward / Block (SALs) | Stakers Reward / 21,600 (SALs) | Yield (%) |
|-----------|-------------------------------|--------------------------------|-----------|
| 4,800,000 | 30 | 648,000 | 13.50 |
| 3,000,000 | 30 | 648,000 | 21.60 |
| 4,000,000 | 30 | 648,000 | 16.20 |
| 5,000,000 | 30 | 648,000 | 12.96 |
| 6,000,000 | 30 | 648,000 | 10.80 |
| 7,000,000 | 30 | 648,000 | 9.26 |
| 8,000,000 | 30 | 648,000 | 8.10 |
| 9,000,000 | 30 | 648,000 | 7.20 |
| 10,000,000 | 30 | 648,000 | 6.48 |

These examples show how the yield percentage decreases as more SAL is staked (higher TVL). It's important to understand that these figures are hypothetical and based on current reward rates. Actual yields will vary due to fluctuations in TVL and changes in block rewards.

## Key Considerations

* **Burnt Coins:** The yield system accounts for coins that are burnt or not claimed. For the past 30 days, 0.00000000 SAL has been burnt.
* **Yield Variability:** Actual yield depends on fluctuating TVL during the staking period.
* **Visible Rewards:** Only 80% of the total block reward (miners' portion) is published; the yield portion is an additional 20%.
* **Dynamic Rates:** Yield rates can change due to fluctuations in TVL and adjustments in block rewards.
* **No Guarantees:** Historical data and current rates do not predict future yields, as the system adapts to network conditions and participation levels.