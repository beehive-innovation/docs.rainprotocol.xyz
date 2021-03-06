---
layout: single
title:  "Gating Your Sale with NFTs"
date:   2022-04-14 14:00:23 +0000
categories: various
---

## Intro

_(Alpha Version)_

We will now begin to combine knowledge gathered from [previous tutorials][gated-nft-tutorial] in order to show how composibility works within the [Rain][rain-protocol] ecosystem.

In a [previous tutorial][sale-tutorial], we created a Sale which allowed users to buy custom Digital Objects (rTKN), in exchange for other Digital Objects (a Digital Currency).

If you remember, the Sale was configured using a pre existing Tier contract even though we didn't specify any configuration for this. We will now deploy another Sale, but this time use a custom Tier system.


## Overview

As an overview, this tutorial will show you how to do the following in this order: 

1. Create an Asset (Digital Object/NFT) which the User is required to own in order to take part in the Sale.
2. Create a Tier 'barrier' which connects with the Asset and is responsible for allowing (or not) access to the Sale based on ownership.
3. Create the Sale and connect the Tier system (which is connected to the Asset). The Sale will then be using this system when users want to take part.

Finally:
* We will demonstrate the Sale failing if the User (you) do not have the required access token.
* We will mint an Asset for the User (i.e. we will send you one of these NFTs).
* We will demonstrate the Sale passing after the User is given an access token (i.e. we will allow you to buy from the Sale).

As always, you will need [Polygon Mumbai Testnet Matic][polygon-faucet] tokens for this tutorial, for both network fees AND for buying from the Sale. 

## Creating the Asset for use with Tier Gating the Sale

If you followed the [GatedNFT tutorial][gated-nft-tutorial], we created a custom NFT and specified parameters for how it should work. We will now create another NFT for token gating a Sale.

As before, we will need the [importmap boilerplate][importmap-boilerplate] set up which is agnostic to any framework (such as React), but you are welcome to follow this example using a framework of your choice ([see an example of how to use this tutorial with React][gated-sale-frontend]).

[//]: # (// todo remember to remind user to approve transactions at each stage &#40;may need to click off and back on to metamask&#41;)
[//]: # (// todo remember to remind user who they will be at each point &#40;dev vs user&#41;)

For this tutorial, we are going to create 3 separate Javascript files, in addition to `index.js`, in order to keep things clean, and to show more easily what configuration belongs to what (it should also allow you to easily see what parts of the system are connected to each tutorial).

Create a new file called `deployGatedNFT.js` and make sure to import it into `index.js` like so: `import deployGatedNFT from "./deployGatedNFT.js";`

This file will be very similar to this previous tutorial, and we won't go into much detail about what is going on, as that is covered over there:

```javascript
import * as rainSDK from "rain-sdk";

export default async function deployGatedNFTContract(signer) {
  const address = await signer.getAddress()

  // config for our (not really gated) NFT used in gating
  const gatedNFTState = {
    config: {
      name: 'My Access Token',
      symbol: 'mAT',
      description: 'My Access Token can give me access to things',
      animationUrl: '',
      animationHash: '0x0000000000000000000000000000000000000000000000000000000000000000',
      imageUrl: 'https://thumbs.dreamstime.com/b/gold-badge-5392868.jpg',
      imageHash: '0x0000000000000000000000000000000000000000000000000000000000000000',
    },
    tier: '0xcd953b94999808ee07a33860dd46689580c90cf4', // this config is needed? // todo check this config
    minimumStatus: 0,
    maxPerAddress: 1, // let's only let people have 2 for this tutorial
    transferrable: 0,
    maxMintable: 1, // for the purposes of this tutorial, we are only allowing there to be 1 token is existance
    royaltyRecipient: "",
    royaltyBPS: 10
  }
  gatedNFTState.royaltyBPS = BigNumber.from(Math.floor(gatedNFTState.royaltyBPS * 100)); // convert royaltyBPS to BigNumber format
  gatedNFTState.royaltyRecipient = address; // set YOU to be the recipient

  console.log("Creating: GatedNFT for Gating Sale with the following State:", gatedNFTState,);
  const gatedNFTContract = await rainSDK.GatedNFT.deploy(signer, gatedNFTState); // todo should this be then passed to the constructor in the sdk or used as is?
  console.log(`Result: GatedNFT Contract`, gatedNFTContract);
  return gatedNFTContract;
}
```

The major difference here is that we have changed the configuration to be `maxPerAddress: 1` and `maxMintable: 1`. It should be obvious what this does, (1 user gets one ticket), and since we are only ever going to give a ticket to ourselves, we set a `maxMintable` value.

We can now add code for calling the deploy into `index.js`:

```javascript
// Deploy gated NFT
const gatedNFTContract = deployGatedNFTContract(signer);
```


## Creating the Tier Contract for use with Gating the Sale

Now we need to link the required ticket-like NFT to the Tier.

As before, we will create a new file called `deployTier.js` and import it into `index.js` like so: `import deployTier from "./deployTier.js";`.

This tier contract needs to use the Gated NFT created previously, so must include `gatedNFTContract` as a parameter:

```javascript
import * as rainSDK from "rain-sdk";

export default async function deployTierContract(signer, gatedNFTContract) {
  const tierState = {
    erc721: gatedNFTContract.address, // this will be set to the address of the GatedNFT
    // tier 1 means the user needs 1 token, tier 2, 2 tokens (however, we have set users to only be allowed one, so only tier 1 works here)
    tierValues: [1,2,2,2,2,2,2,2] // any tiers beyond 1 won't matter because there will only ever be one of these tokens in existence
  }

  console.log("Creating: Tier Contract (using GatedNFT) with the following State:", tierState);
  const tierContract = await rainSDK.ERC721BalanceTier.deploy(signer, tierState); // todo should this be then passed to the constructor in the sdk or used as is?
  console.log(`Result: Tier Contract`, tierContract);
  return tierContract;
}
```

We set the `erc721` address here to the address of the Gated NFT Contract, and set the tierValues as follows: `[1,2,2,2,2,2,2,2]`.

Currently, to deploy a tiering system, 8 different parameters need to be passed, representing an amount of `erc721` tokens of the type configured above.

We set `tier1` to be `1` token, and this is the main tier we will use. The rest of the values, we set to `2` which will be impossible to achieve because previously we set the  `maxPerAddress` and `maxMintable` values of the token itself to 1 (so it will be impossible for anyone to own more, and therefore achieve a tier higher than 1).

We can now deploy the Tier in `index.js` as we did with the Gated NFT:

```javascript
// Deploy Tier Contract to be used in Sale
const tierContract = deployTierContract(signer, gatedNFTContract);
console.log('------------------------------'); // separator
```

## Creating and Tier Gating the Sale

[//]: # (// todo write that this is an ERC721 Balance Tier Contract and that it is possible to Combine Tiers)

We will now add the Sale and pass in the Tier contract. Again, we won't go over what is happening here, as we covered this in another tutorial.

Create `deploySale.js` and import as previously: `import deploySale from "./deploySale.js";`

And then create the functionality for the Sale itself:

```javascript
import * as rainSDK from "rain-sdk";

export default async function deploySale(signer, tierContract) {
  const address = await signer.getAddress()

  // config for the sale
  const erc20decimals = 18; // See here for more info: https://docs.openzeppelin.com/contracts/3.x/erc20#a-note-on-decimals
  const staticPrice = ethers.utils.parseUnits("100", erc20decimals);
  const walletCap = ethers.utils.parseUnits("10", erc20decimals);
  const saleState = {
    canStartStateConfig: undefined, // config for the start of the Sale (see opcodes section below)
    canEndStateConfig: undefined, // config for the end of the Sale (see opcodes section below)
    calculatePriceStateConfig: undefined, // config for the `calculatePrice` function (see opcodes section below)
    recipient: "", // who will receive the RESERVE token (e.g. Matic/USDCC) after the Sale completes
    reserve: "0x0000000000000000000000000000000000001010", // the reserve token contract address (Polygon Testnet MATIC)
    saleTimeout: 100, // this will be 100 blocks
    cooldownDuration: 100, // this will be 100 blocks
    minimumRaise: ethers.utils.parseUnits("1000", erc20decimals), // minimum to complete a Raise
    dustSize: ethers.utils.parseUnits("0", erc20decimals),
  };
  const redeemableState = {
    erc20Config: { // config for the redeemable token (rTKN) which participants will get in exchange for reserve tokens
      name: "Raise token", // the name of the rTKN
      symbol: "rTKN", // the symbol for your rTKN
      distributor: "0x0000000000000000000000000000000000000000", // distributor address
      initialSupply: ethers.utils.parseUnits("1000", erc20decimals), // initial rTKN supply
    },
    tier: tierContract.address, // tier contract address (used for gating), will be set to the address of the tier contract after deployment
    minimumTier: 0, // minimum tier a user needs to take part
    distributionEndForwardingAddress: "0x0000000000000000000000000000000000000000" // the rTKNs that are not sold get forwarded here (0x00.. will burn them)
  }

  // Opcode Configurations
  saleState.canStartStateConfig = {
    constants: [1],
    sources: [ethers.utils.concat([ rainSDK.VM.op(rainSDK.Sale.Opcodes.VAL, 0) ])],
    stackLength: 1,
    argumentsLength: 0,
  };
  saleState.canEndStateConfig = {
    constants: [1],
    sources: [ethers.utils.concat([ rainSDK.VM.op(rainSDK.Sale.Opcodes.VAL, 0) ])],
    stackLength: 1,
    argumentsLength: 0,
  };

  // define the parameters for the VM which will be used whenever the price is calculated, for example, when a user wants to buy a number of units
  saleState.calculatePriceStateConfig = {
    constants: [staticPrice, walletCap, ethers.constants.MaxUint256],
    sources: [
      ethers.utils.concat([
        rainSDK.VM.op(rainSDK.Sale.Opcodes.CURRENT_BUY_UNITS),
        rainSDK.VM.op(rainSDK.Sale.Opcodes.TOKEN_ADDRESS),
        rainSDK.VM.op(rainSDK.Sale.Opcodes.SENDER),
        rainSDK.VM.op(rainSDK.Sale.Opcodes.IERC20_BALANCE_OF),
        rainSDK.VM.op(rainSDK.Sale.Opcodes.ADD, 2),
        rainSDK.VM.op(rainSDK.Sale.Opcodes.VAL, 1),
        rainSDK.VM.op(rainSDK.Sale.Opcodes.GREATER_THAN),
        rainSDK.VM.op(rainSDK.Sale.Opcodes.VAL, 2),
        rainSDK.VM.op(rainSDK.Sale.Opcodes.VAL, 0),
        rainSDK.VM.op(rainSDK.Sale.Opcodes.EAGER_IF),
      ]),
    ],
    stackLength: 10,
    argumentsLength: 0,
  };
  saleState.recipient = address;

  console.log("Creating: Sale (Using Tier contract which uses GatedNFT) with the following State:", saleState, redeemableState);
  const saleContract = await rainSDK.Sale.deploy(signer, saleState, redeemableState); // todo should this be then passed to the constructor in the sdk or used as is?
  console.log('Result: Sale Contract:', saleContract); // the Sale contract and corresponding address
  return saleContract;
}
```
Here we can see that we have set the `tier` parameter on the ERC20 being sold in the Sale, to be the tier contract of the Tier we just created: `tier: tierContract.address`.

We have also set the `reserve` in the Sale (i.e the token-to-gather) to be the Contract address of [Mumbai TestNet Matic][polygon-faucet], which we will be gathering for this example.

We can now create this Sale in `index.js`:

```javascript
// Deploy Sale
const saleContract = deploySale(signer, tierContract);
console.log('------------------------------'); // separator
```

## Interacting with what we have done

Now that we have our Gated Sale deployed, we want to test that it works.

First off, we will get the `price` of tokens in the Sale and create a buy configuration:

```javascript
let price = await saleContract.calculatePrice(ethers.utils.parseUnits("100", erc20decimals));
console.log(`Info: Price of tokens in the Sale: ${price}`); // todo check the price is correct

// configure buy for the sale (We have set this to Matic which is also used for paying gas fees, but this could easily be set to usdcc or some other token)
const buyConfig = {
  feeRecipient: address,
  fee: price*0.01, // 1 percent fee for the platform
  minimumUnits: 1000000, // 1 million??
  desiredUnits: 1000000,
  maximumPrice: price*10, // TODO VERY ARBITRARY ETHERS CONSTANT MAX AMOUNT // todo why do we set this?
}
```

`fee` is an amount which platforms can set to take a commission of a Sale, the `feeRecipient` is who gets the fee.

### Testing that a buy fails

We now want to check that a buy fails (as we don't have the required tiket-like NFT for taking part):

```javascript
try { // separate try block as we want to catch the error separately
  console.log(`Info: Buying from Sale with parameters:`, buyConfig);
  await saleContract.buy(buyConfig); // this should trigger the catch below
} catch (err) {
  console.log(`Info: This should have failed because you don't have one of the NFTs required for taking part`, err); // console log the error which should be a revert
}
```

Here we have created a separate `try` block to catch the error so that the we can handle how the program responds to the error, i.e. that it continues and tries again after the User has the required NFT.

### Testing that a buy passes

Finally, we want to give the User (you) an NFT and check that the buy passes:

```javascript
console.log(`Info: Minting you a required NFT to take part in Sale:`); // mint and send to you
const result = await gatedNFTContract.mint(address); // get one of the NFTs needed to take part in the sale
console.log(`Result: of NFT Minting:`, result);

console.log(`Info: Buying from Sale with parameters:`, buyConfig);
const buyStatus = await saleContract.buy(buyConfig);
console.log(`Info: This should have passed because you do have one of the NFTs required for taking part`, buyStatus);
```

## Conclusion

And that is a wrap on how you can connect different Rain components together. As always, if you have questions please reach out, and you can [become part of our community by joining here][discord].

[Here is a demo on how it might work with a React frontend][gated-sale-frontend].

[rain-protocol]: https://rainprotocol.xyz
[full-example]: https://github.com/unegma/sdk-tutorial-sale
[sale]: https://docs.rainprotocol.xyz/smart-contracts/sale/
[token-gating]: https://medium.com/@jshanks21/nft-meaning-token-gating-ad83aef7cccd
[telegram]: https://t.me/+w4mJbCT6IfI2YTU0
[docs]: https://docs.rainprotocol.xyz
[react-example]: https://github.com/beehive-innovation/examples.rainprotocol.xyz/blob/master/src/examples/DeploySaleExample/DeploySaleExample.tsx
[react-example-live]:  https://examples.rainprotocol.xyz/deploy-sale-example
[unpkg]: https://unpkg.com/
[polygon-faucet]: https://faucet.polygon.technology/
[metamask-tutorial]: https://www.youtube.com/watch?v=6h_liI6atEk
[system-js]: https://www.digitalocean.com/community/tutorials/how-to-dynamically-import-javascript-with-import-maps
[npx]: https://stackoverflow.com/questions/50605219/difference-between-npx-and-npm
[rain-sdk]: https://github.com/beehive-innovation/rain-sdk
[ethers]: https://github.com/ethers-io/ethers.js/
[full-example]: https://github.com/unegma/sdk-tutorial-sale
[opcodes]: https://en.wikipedia.org/wiki/Opcode
[sale-tutorial]: https://docs.rainprotocol.xyz/guides/Getting%20Started/using-the-rain-sdk-to-deploy-a-sale-example-with-opcodes
[gated-nft-tutorial]: https://docs.rainprotocol.xyz/guides/Getting%20Started/using-the-rain-sdk-to-deploy-your-first-rain-contract
[importmap-boilerplate]: https://github.com/unegma/javascript-importmap-template
[gated-sale-frontend]: https://github.com/unegma/gated-sale-frontend
