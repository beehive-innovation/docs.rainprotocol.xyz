<!-- Do not edit this file. It is automatically generated by API Documenter. -->

[Home](./index.md) &gt; [rain-sdk](./rain-sdk.md) &gt; [ERC20BalanceTier](./rain-sdk.erc20balancetier.md)

## ERC20BalanceTier class

A class for deploying and calling methods on a ERC20BalanceTier.

The `ERC20BalanceTier` simply checks the current balance of an erc20 against tier values. As the current balance is always read from the erc20 contract directly there is no historical block data.

<b>Signature:</b>

```typescript
export declare class ERC20BalanceTier extends TierFactoryContract 
```
<b>Extends:</b> [TierFactoryContract](./rain-sdk.tierfactorycontract.md)

## Remarks

This class provides an easy way to deploy ERC20BalanceTiers using Rain's canonical factories, and methods for interacting with an already deployed ERC20BalanceTier.

## Example


```typescript
import { ERC20BalanceTier } from 'rain-sdk'

// To deploy a new ERC20BalanceTier, pass an ethers.js Signer and the config for the ERC20BalanceTier.
const newTier = await ERC20BalanceTier.deploy(signer, ERC20BalanceTierArgs);

// To connect to an existing ERC20BalanceTier just pass the tier address, token address and an ethers.js Signer.
const existingTier = new ERC20BalanceTier(address, tokenAddrss, signer);

// Once you have a ERC20BalanceTier, you can call the smart contract methods:
const tierValues = await existingTier.tierValues();
```

## Constructors

|  Constructor | Modifiers | Description |
|  --- | --- | --- |
|  [(constructor)(address, tokenAddress, signer)](./rain-sdk.erc20balancetier._constructor_.md) |  | Constructs a new ERC20BalanceTier from a known address. |

## Properties

|  Property | Modifiers | Type | Description |
|  --- | --- | --- | --- |
|  [connect](./rain-sdk.erc20balancetier.connect.md) |  | (signer: Signer) =&gt; [ERC20BalanceTier](./rain-sdk.erc20balancetier.md) |  |
|  [deploy](./rain-sdk.erc20balancetier.deploy.md) | <code>static</code> | (signer: Signer, args: [ERC20BalanceTierDeployArgs](./rain-sdk.erc20balancetierdeployargs.md)<!-- -->, overrides?: [TxOverrides](./rain-sdk.txoverrides.md)<!-- -->) =&gt; Promise&lt;[ERC20BalanceTier](./rain-sdk.erc20balancetier.md)<!-- -->&gt; | Deploys a new ERC20BalanceTier. |
|  [isChild](./rain-sdk.erc20balancetier.ischild.md) | <code>static</code> | (signer: Signer, maybeChild: string) =&gt; Promise&lt;boolean&gt; | Checks if address is registered as a child contract of this ERC20BalanceTierFactory on a specific network |
|  [nameBookReference](./rain-sdk.erc20balancetier.namebookreference.md) | <code>static</code> | (not declared) |  |
|  [setTier](./rain-sdk.erc20balancetier.settier.md) |  | (account: string, endTier: BigNumberish, data: BytesLike, overrides?: [TxOverrides](./rain-sdk.txoverrides.md) \| undefined) =&gt; Promise&lt;never&gt; | It is NOT implemented in BalanceTiers. Always will throw an error |
|  [tierValues](./rain-sdk.erc20balancetier.tiervalues.md) |  | (overrides?: [ReadTxOverrides](./rain-sdk.readtxoverrides.md)<!-- -->) =&gt; Promise&lt;BigNumber\[\]&gt; | Complements the default solidity accessor for <code>tierValues</code>. Returns all the values in a listrather than requiring an index be specified. |
|  [token](./rain-sdk.erc20balancetier.token.md) |  | string | ERC20 Token address that track the Tier |

## Methods

|  Method | Modifiers | Description |
|  --- | --- | --- |
|  [amountToTier(desiredLevel, account)](./rain-sdk.erc20balancetier.amounttotier.md) |  | Calculate how much amount of the token needed transfer to or transfer out of the account to reach a <code>desiredLevel</code>.<!-- -->Take in mind: - If the <code>desired level</code> is higher than the current level, the amount returned will be the amount needed to obtain or transfer to the <code>account</code>. - If the <code>desired level</code> is lower than the current level, the amount returned will be the amount needed to remove or transfer out of the <code>account</code>. - If already have the <code>desired</code> tier, will return 0 |
