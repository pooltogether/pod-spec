# PoolTogether Pods

[PoolTogether](https://github.com/pooltogether) is about helping people save by providing fun prize incentives.  The size of a user’s deposit is proportional to their chance of winning.  Many of our users have smaller deposits in the < $500 range, but with a pool of $35m their chances of winning are very low.  We’re going to change that with Pods.

Pods allow users to pool their deposits together to increase their chances of winning and instead take a share of the prize.  This will allow the smaller “fish” to win more often; rather than having the whale take all of the funds.

# Pod Specification

A full description of the Pod interface is available in [IPod.sol](./contracts/IPod.sol).

When a user deposits into a Pod, they are minted **shares** of the Pod.  The Pod then deposits the funds into a Prize Pool for a chance to win the prize.  As the Pod wins prizes, each Pod share is worth more as the pool of PrizePool tickets it holds grows larger.

This specification will be the starting point for an official PoolTogether Pod reference contract.  There are three major elements to the spec:

- **Batching:** deposits should be cheap.  Batching deposits will make depositing into Pods much cheaper than the Prize Pools themselves.
- **POOL Distribution:** Pod users should be eligible for POOL token distribution, if any.
- **Liquidation Strategies:** Pods should support the ability for strategies to liquidate random tokens that accrue in the pool.  Note that this spec does not define **how** the liquidation should occur, it just makes provisions for it.

## Batching

Pods are intended to be used by small fish, which means the transaction fees should be as cheap as possible.  A common pattern to enable cheap deposits is to batch deposits together, similar to Yearn Vaults, Balancer V2, and the [PoolPower project](https://github.com/PoolPower/poolpower-contracts-v1).  Batching means that when a user deposits into a Pod the deposit is held by the Pod contract.  Later, once deposits have built up, someone can deposit all of the funds all at once.

The deposit flow goes like so:

1. Users deposit into the Pod.  The funds are held in the Pod as the 'float'.
2. A user triggers a batch deposit on the pods; depositing the float into the underlying protocol.

The withdrawal flow looks like so:

1. User withdraws a portion of their Pod shares.
2. The current underyling value of the shares is calculated.
3. The user is first paid out of the 'float'

## POOL distribution

Prize Pools may sometimes reward depositors with POOL tokens for supplying liquidity.  These POOL tokens should be split among the Pod share holders so they can build ownership in PoolTogether governance.

## Liquidation Strategies

Pods may accrue random ERC20 and ERC721 tokens as a result of being entered into a Prize Pool.  It may be possible to split ERC20 tokens up, but ERC721s are not possible to split up. Instead, it would be useful to bake into Pods the ability for a separate contract to liquidate tokens and re-distribute the capital across Pod share holders.

There are two elements to this:

- Privileged ability to withdraw ERC20 and ERC721 tokens from the Pod.  Tokens that are critical to the Pod share holders, such as the deposit token, Prize Pool tickets, or POOL tokens should be disallowed from withdrawals.
- Ability for anyone to deposit into the Pod without receiving shares.  This allows an external contract to distribute capital across all of the Pod share holders.