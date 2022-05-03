<!-- Do not edit this file. It is automatically generated by API Documenter. -->

[Home](./index.md) &gt; [rain-sdk](./rain-sdk.md) &gt; [Sale](./rain-sdk.sale.md) &gt; [buy](./rain-sdk.sale.buy.md)

## Sale.buy property

Main entrypoint to the sale. Sells rTKN in exchange for reserve token. The price curve is eval'd to produce a reserve price quote. Each 1 unit of rTKN costs `price` reserve token where BOTH the rTKN units and price are treated as 18 decimal fixed point values. If the reserve token has more or less precision by its own conventions (e.g. "decimals" method on ERC20 tokens) then the price will need to scale accordingly. The receipt is \_logged\_ rather than returned as it cannot be used in same block for a refund anyway due to cooldowns.

<b>Signature:</b>

```typescript
readonly buy: (config: BuyConfig, overrides?: TxOverrides) => Promise<ContractTransaction>;
```