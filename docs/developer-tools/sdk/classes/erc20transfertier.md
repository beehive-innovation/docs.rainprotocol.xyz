
# Class ERC20TransferTier

A class for deploying and calling methods on a ERC20TransferTier.

This class provides an easy way to deploy ERC20TransferTiers using Rain's canonical factories, and methods for interacting with an already deployed ERC20TransferTier.

The `ERC20TransferTier` takes ownership of an erc20 balance by transferring erc20 token to itself. The `msg.sender` must pay the difference on upgrade; the tiered address receives refunds on downgrade. This allows users to "gift" tiers to each other. As the transfer is a state changing event we can track historical block times.

As the tiered address moves up/down tiers it sends/receives the value difference between its current tier only.The user is required to preapprove enough erc20 to cover the tier change or they will fail and lose gas.

In addition to the standard accounting it requires that users transfer erc20 tokens to achieve a tier. Data is ignored, the only requirement is that the user has approved sufficient balance to gain the next tier. The 8 values for gainable tiers and erc20 contract must be set upon construction and are immutable.

`⚠️ WARNING:` If a user sends erc20 tokens directly to the contract without calling `setTier` the FUNDS ARE LOST. ⚠️

<b>Signature:</b>

```typescript
class ERC20TransferTier extends TierContract 
```

## Example


```typescript
import { ERC20TransferTier } from 'rain-sdk'

// To deploy a new ERC20TransferTier, pass an ethers.js Signer and the config for the ERC20TransferTier.
const newTier = await ERC20TransferTier.deploy(signer, ERC20TransferTierConfigArgs);

// To connect to an existing ERC20TransferTier just pass the tier address, token address and an ethers.js Signer.
const existingTier = new ERC20TransferTier(address, tokenAddress, signer);

// Once you have a ERC20TransferTier, you can call the smart contract methods:
const tierValues = await existingTier.tierValues();

```

## Static Properties

|  Property | Type | Description |
|  --- | --- | --- |
|  [getAddressesForChainId](./addressbook.md#getAddressesForChainId-property-static) | `(chainId: number) => Addresses` | Obtain all the addresses deployed in a specific network with a chain ID.<br></br>*Inherited from [AddressBook.getAddressesForChainId](./addressbook.md#getAddressesForChainId-property-static)* |
|  [getChainId](./raincontract.md#getChainId-property-static) | `(signerOrProvider: Signer \| Provider) => Promise<number>` | Get the chain ID from a valid ethers provider.<br></br>Request to the provider stored in the signer which is the chain ID.<br></br>*Inherited from [RainContract.getChainId](./raincontract.md#getChainId-property-static)* |
|  [getSubgraphEndpoint](./addressbook.md#getSubgraphEndpoint-property-static) | `(chainId: number) => string` | Obtain the latest subgraph endpoint related to the version that use the SDK.<br></br>*Inherited from [AddressBook.getSubgraphEndpoint](./addressbook.md#getSubgraphEndpoint-property-static)* |
|  [isChild](./erc20transfertier.md#isChild-property-static) | `(signer: Signer, maybeChild: string) => Promise<boolean>` | Checks if address is registered as a child contract of this ERC20TransferTierFactory on a specific network |
|  [nameBookReference](./erc20transfertier.md#nameBookReference-property-static) | `string` | Name reference to find the address of the contract in the book address.<br></br>*Overrides [RainContract.nameBookReference](./raincontract.md#nameBookReference-property-static)* |

## Properties

|  Property | Type | Description |
|  --- | --- | --- |
|  [address](./raincontract.md#address-property) | `string` | The contract address of the instance.<br></br>*Inherited from [RainContract.address](./raincontract.md#address-property)* |
|  [addTokenAddress](./erc20transfertier.md#addTokenAddress-property) | `(tokenAddress: string) => ERC20TransferTier` | Get a new instance with the token address provided.<br></br>This method must be used if the token address is not provided in construction moment. The class use this address to make calculations related with the Tier. The token address provided should be the same that the Tier is using to work correctly. |
|  [connect](./erc20transfertier.md#connect-property) | `(signer: Signer) => ERC20TransferTier` | Connect the current contract instance to a new ethers signer.<br></br>*Overrides [RainContract.connect](./raincontract.md#connect-property)* |
|  [levels](./tiercontract.md#levels-property) | `typeof Tier` | All the contract tier levels availables in all ITier contracts.<br></br>*Inherited from [TierContract.levels](./tiercontract.md#levels-property)* |
|  [report](./tiercontract.md#report-property) | `(account: string, overrides?: ReadTxOverrides) => Promise<BigNumber>` | A tier report is a `uint256` that contains each of the block numbers each tier has been held continously since as a `uint32`<!-- -->.<br></br>*Inherited from [TierContract.report](./tiercontract.md#report-property)* |
|  [setTier](./erc20transfertier.md#setTier-property) | `(account: string, endTier: BigNumberish, data: BytesLike, overrides?: TxOverrides) => Promise<ContractTransaction>` | Updates the tier of an account tansfering balances of erc20 from/to the tiered account according to the difference in values.<br></br>*Overrides [TierContract.setTier](./tiercontract.md#setTier-property)* |
|  [signer](./raincontract.md#signer-property) | `Signer` | The ethers signer that is connected to the instance.<br></br>*Inherited from [RainContract.signer](./raincontract.md#signer-property)* |
|  [tierValues](./erc20transfertier.md#tierValues-property) | `(overrides?: ReadTxOverrides) => Promise<BigNumber[]>` | Complements the default solidity accessor for `tierValues`<!-- -->. Returns all the values in a listrather than requiring an index be specified. |
|  [token](./erc20transfertier.md#token-property) | `string` | The ERC20 Token address that holds the Tier. |

## Static Methods

|  Method | Description |
|  --- | --- |
|  [\_isChild(signer, maybeChild)](./factorycontract.md#_isChild-method-static-1) | Checks if address is registered as a child contract of the factory in the chain.<br></br>*Inherited from [FactoryContract.\_isChild()](./factorycontract.md#_isChild-method-static-1)* |
|  [deploy(signer, args, overrides)](./erc20transfertier.md#deploy-method-static-1) | Deploys a new ERC20TransferTier. |
|  [getBookAddress(chainId)](./raincontract.md#getBookAddress-method-static-1) | Get the address stored in the book for a determined chain if it is available.<br></br>*Inherited from [RainContract.getBookAddress()](./raincontract.md#getBookAddress-method-static-1)* |
|  [getNewChildFromReceipt(receipt, parentContract)](./factorycontract.md#getNewChildFromReceipt-method-static-1) | Get the child from a receipt obtain from a Factory transaction<br></br>*Inherited from [FactoryContract.getNewChildFromReceipt()](./factorycontract.md#getNewChildFromReceipt-method-static-1)* |

## Methods

|  Method | Description |
|  --- | --- |
|  [amountToTier(account, desiredLevel)](./erc20transfertier.md#amountToTier-method-1) | Calculate how much amount of the token needed transfer to the tier contract or how much tokens the `account` will get back to reach the `desiredLevel`<!-- -->. |
|  [approveTokenForTier(amount, overrides)](./erc20transfertier.md#approveTokenForTier-method-1) |  |
|  [checkAddress(address, message)](./raincontract.md#checkAddress-method-1) | Check if an address is correctly formatted and throw an error if it is not an valid address<br></br>*Inherited from [RainContract.checkAddress()](./raincontract.md#checkAddress-method-1)* |
|  [currentTier(account, block)](./tiercontract.md#currentTier-method-1) | Get the current tier of an `account` in the Tier as an expression between `[0 - 8]`<!-- -->. Tier 0 is that a address has never interact with the Tier Contract.<br></br>*Inherited from [TierContract.currentTier()](./tiercontract.md#currentTier-method-1)* |

## Static Property Details

<a id="isChild-property-static"></a>

### isChild

Checks if address is registered as a child contract of this ERC20TransferTierFactory on a specific network

<b>Signature:</b>

```typescript
static isChild: (signer: Signer, maybeChild: string) => Promise<boolean>;
```

<a id="nameBookReference-property-static"></a>

### nameBookReference

Name reference to find the address of the contract in the book address.

*Overrides [RainContract.nameBookReference](./raincontract.md#nameBookReference-property-static)*

Should be implemented in each class to find the factory or main address in the book.

<b>Signature:</b>

```typescript
protected static readonly nameBookReference: string;
```

## Property Details

<a id="addTokenAddress-property"></a>

### addTokenAddress

Get a new instance with the token address provided.

This method must be used if the token address is not provided in construction moment. The class use this address to make calculations related with the Tier. The token address provided should be the same that the Tier is using to work correctly.

<b>Signature:</b>

```typescript
readonly addTokenAddress: (tokenAddress: string) => ERC20TransferTier;
```

<a id="connect-property"></a>

### connect

Connect the current contract instance to a new ethers signer.

*Overrides [RainContract.connect](./raincontract.md#connect-property)*

<b>Signature:</b>

```typescript
readonly connect: (signer: Signer) => ERC20TransferTier;
```

<a id="setTier-property"></a>

### setTier

Updates the tier of an account tansfering balances of erc20 from/to the tiered account according to the difference in values.

*Overrides [TierContract.setTier](./tiercontract.md#setTier-property)*

Users can set their own tier by calling `setTier`<!-- -->.

Any failure to transfer in/out will rollback the tier change. The tiered account must ensure sufficient approvals before attempting to set a new tier. This throw an error if the user attempts to return to the ZERO tier.

<b>Signature:</b>

```typescript
readonly setTier: (account: string, endTier: BigNumberish, data: BytesLike, overrides?: TxOverrides) => Promise<ContractTransaction>;
```

<a id="tierValues-property"></a>

### tierValues

Complements the default solidity accessor for `tierValues`<!-- -->. Returns all the values in a listrather than requiring an index be specified.

<b>Signature:</b>

```typescript
readonly tierValues: (overrides?: ReadTxOverrides) => Promise<BigNumber[]>;
```

<a id="token-property"></a>

### token

The ERC20 Token address that holds the Tier.

<b>Signature:</b>

```typescript
readonly token: string;
```

## Static Method Details

<a id="deploy-method-static-1"></a>

### deploy(signer, args, overrides)

Deploys a new ERC20TransferTier.

<b>Signature:</b>

```typescript
static deploy(signer: Signer, args: ERC20TransferTierDeployArgs, overrides?: TxOverrides): Promise<ERC20TransferTier>;
```

#### Parameters

|  Parameter | Type | Description |
|  --- | --- | --- |
|  signer | `Signer` | An ethers.js Signer |
|  args | [ERC20TransferTierDeployArgs](../interfaces/erc20transfertierdeployargs.md) | Arguments for deploying a ERC20TransferTier |
|  overrides | [TxOverrides](../interfaces/txoverrides.md) |  |

<b>Returns:</b>

`Promise<ERC20TransferTier>`

A new ERC20TransferTier instance

## Method Details

<a id="amountToTier-method-1"></a>

### amountToTier(account, desiredLevel)

Calculate how much amount of the token needed transfer to the tier contract or how much tokens the `account` will get back to reach the `desiredLevel`<!-- -->.

Take in mind: - If the `account` directly send tokens to the ERC20TransferTier contract, those tokens are lost. All the calcualtions are make it with the tokens transfered by the account with setTier method. - If the `desired level` is higher than the current level, the amount returned will be the amount needed to obtain tier. - If the `desired level` is lower than the current level, the amount returned will be the amount that will get back. - If already have the `desired` tier, will return 0.

<b>Signature:</b>

```typescript
amountToTier(account: string, desiredLevel: number): Promise<BigNumber>;
```

#### Parameters

|  Parameter | Type | Description |
|  --- | --- | --- |
|  account | `string` | (optional) the account address to calculate. If not provided will use the signer of the instance |
|  desiredLevel | `number` | the tier level desired to get |

<b>Returns:</b>

`Promise<BigNumber>`

The amount t

<a id="approveTokenForTier-method-1"></a>

### approveTokenForTier(amount, overrides)

<b>Signature:</b>

```typescript
approveTokenForTier(amount: BigNumberish, overrides?: ReadTxOverrides): Promise<ContractTransaction>;
```

#### Parameters

|  Parameter | Type | Description |
|  --- | --- | --- |
|  amount | `BigNumberish` |  |
|  overrides | [ReadTxOverrides](../interfaces/readtxoverrides.md) |  |

<b>Returns:</b>

`Promise<ContractTransaction>`

