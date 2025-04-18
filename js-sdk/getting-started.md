---
description: Getting started with the Hop v2 JavaScript SDK
---

# Getting started

## Install module

Using NPM:

```bash
npm install @hop-protocol/v2-sdk
```

Using Yarn:

```bash
yarn add @hop-protocol/v2-sdk
```

## CDN

[jsDeliver](https://www.jsdelivr.com/) CDN:

```html
<script src="https://cdn.jsdelivr.net/npm/@hop-protocol/v2-sdk@latest/hop.js"></script>
```

[unpkg](https://unpkg.com/) CDN:

```html
<script src="https://unpkg.com/@hop-protocol/v2-sdk@latest/hop.js"></script>
```

## Import module

Import as ES6 module (e.g. Using TypeScript, babel, webpack):

```javascript
import { Hop } from '@hop-protocol/v2-sdk'
```

Import as commonJS module (e.g. Using Node.js directly or no ES6 modules):

```javascript
const { Hop } = require('@hop-protocol/v2-sdk')
```

## Instantiate SDK

```javascript
import { Hop } from '@hop-protocol/v2-sdk'

const hop = new Hop({ network: 'sepolia' })
```

Avaiable networks are only `sepolia` at this time.\
\


## Hop V2 SDK Documentation

The Hop V2 SDK provides a simple interface for interacting with the Hop Protocol V2. This documentation will help you get started with integrating the SDK into your application.

### Table of Contents

* Installation
* SDK Setup
* Complete Transfer Flow
  * 1\. Initialize SDK
  * 2\. Check Token Approval
  * 3\. Send Tokens
  * 4\. Monitor Transfer Status
  * 5\. Handle Transfer Completion
* Additional Functions
* Event Handling
* Types
* Best Practices

### Installation

```bash
npm install @hop-protocol/v2-sdk
```

### SDK Setup

First, initialize the SDK with your network configuration:

```typescript
import { Hop } from '@hop-protocol/v2-sdk'
import { providers } from 'ethers'

const hop = new Hop({
  network: 'mainnet', // or 'sepolia' for testnet
  signersOrProviders: {
    '1': new providers.JsonRpcProvider('ETHEREUM_RPC_URL'),
    '10': new providers.JsonRpcProvider('OPTIMISM_RPC_URL'),
    // Add other chains as needed
  }
})
```

#### Constructor Options

| Parameter          | Type               | Description                                  | Required |
| ------------------ | ------------------ | -------------------------------------------- | -------- |
| network            | string             | Network name ('mainnet' or 'sepolia')        | No       |
| signersOrProviders | SignersOrProviders | Map of chain IDs to ethers signers/providers | Yes      |
| contractAddresses  | Addresses          | Custom contract addresses                    | No       |
| batchBlocks        | number             | Number of blocks to batch in queries         | No       |

### Complete Transfer Flow

Let's walk through the complete process of transferring tokens from one chain to another.

#### 1. Initialize SDK

First, set up your connection and token details:

```typescript
// Token addresses on respective chains
const sourceToken = '0x...'  // Token on source chain
const destToken = '0x...'    // Token on destination chain
const amount = '1000000000000000000' // Amount in wei (1 token with 18 decimals)
const recipient = '0x...'    // Recipient address

// Chain IDs
const fromChainId = '1'      // Ethereum
const toChainId = '10'       // Optimism
```

#### 2. Check Token Approval

Before sending tokens, check if approval is needed:

```typescript
// Check if approval is needed
const needsApproval = await hop.getNeedsApprovalForSendTokens({
  fromChainId,
  toChainId,
  fromToken: sourceToken,
  toToken: destToken,
  amount,
  account: senderAddress
})

if (needsApproval) {
  // Approve tokens
  const approveTx = await hop.approveSendTokens({
    fromChainId,
    toChainId,
    fromToken: sourceToken,
    toToken: destToken,
    amount
  })
  
  // Wait for approval transaction
  await approveTx.wait()
}
```

#### 3. Send Tokens

Calculate fees and send the tokens:

```typescript
// Get the estimated received amount and fees
const sendData = await hop.getSendData({
  fromChainId,
  toChainId,
  fromToken: sourceToken,
  toToken: destToken,
  amount,
  minAmountOut: '0' // We'll calculate this based on slippage
})

// Calculate minAmountOut with 1% slippage
const slippageTolerance = 0.01 // 1%
const minAmountOut = hop.calcAmountOutMin({
  amountOut: sendData.amountOut,
  slippageTolerance
})

// Send tokens
const tx = await hop.sendTokens({
  fromChainId,
  toChainId,
  fromToken: sourceToken,
  toToken: destToken,
  amount,
  to: recipient,
  minAmountOut
})

// Wait for transaction confirmation
const receipt = await tx.wait()
console.log('Send transaction hash:', receipt.transactionHash)
```

#### 4. Monitor Transfer Status

After sending tokens, monitor the transfer status:

```typescript
// Get transfer ID from transaction hash
const transferId = await hop.getTransferIdFromTransactionHash({
  chainId: fromChainId,
  transactionHash: receipt.transactionHash
})

// Monitor status
async function checkTransferStatus() {
  const status = await hop.getTransferStatus({
    transferId,
    fromChainId,
    toChainId
  })

  switch (status.state) {
    case 'PendingBond':
      console.log('Transfer pending bonding...')
      return false
    case 'Bonded':
      console.log('Transfer completed!')
      return true
    case 'NotFound':
      console.log('Transfer not found')
      return false
  }
}

// Poll for status (implement your own polling mechanism)
const interval = setInterval(async () => {
  const isComplete = await checkTransferStatus()
  if (isComplete) {
    clearInterval(interval)
  }
}, 5000)
```

#### 5. Handle Transfer Completion

The transfer can complete in two ways:

**Option A: Bonded Transfer (Automatic)**

If a bonder picks up your transfer, it will automatically be completed on the destination chain. The status will change to 'Bonded'.

**Option B: Manual Claim (If not bonded)**

If the transfer isn't bonded after some time, you can manually claim it:

```typescript
// First, push the claim if needed
const pushClaimTx = await hop.pushClaim({
  transferId,
  fromChainId,
  toChainId
})
await pushClaimTx.wait()

// Then withdraw the claim
const withdrawClaimTx = await hop.withdrawClaim({
  transferId,
  fromChainId,
  toChainId
})
await withdrawClaimTx.wait()
```

### Additional Functions

#### Get Available Routes

```typescript
// Get all available paths for a token
const paths = await hop.getAllRailsPathAddresses(fromChainId)
```

#### Calculate Fees

```typescript
// Get send fee
const fee = await hop.getSendFee({
  fromChainId,
  toChainId,
  fromToken: sourceToken,
  toToken: destToken
})

// Get max bonder fee
const maxBonderFee = await hop.getMaxBonderFee({
  amountIn: amount
})
```

#### Estimate Gas

```typescript
const gasCost = await hop.estimateGasCostForSend({
  from: senderAddress,
  fromChainId,
  toChainId,
  fromToken: sourceToken,
  toToken: destToken,
  amount,
  minAmountOut
})
```

### Event Handling

Monitor transfer-related events:

```typescript
// Listen for TransferSent events
const events = await hop.getEvents({
  eventName: 'TransferSent',
  chainId: fromChainId,
  fromBlock: receipt.blockNumber,
  toBlock: 'latest'
})

// Listen for TransferBonded events
const bondedEvents = await hop.getEvents({
  eventName: 'TransferBonded',
  chainId: toChainId,
  fromBlock: receipt.blockNumber,
  toBlock: 'latest'
})
```

### Types

Key types for transfer operations:

```typescript
enum TransferState {
  PendingBond = 'PendingBond',
  Bonded = 'Bonded',
  NotFound = 'NotFound'
}

type SendData = {
  amountIn: BigNumber
  amountOut: BigNumber
  estimatedReceived: BigNumber
  sendFee: BigNumber
  maxBonderFee: BigNumber
  routeChainIds: string[]
}
```

### More examples

If you'd like to see more examples or have any feedback, message us on [Discord](https://discord.gg/PwCF88emV4)!

### SDK API Reference

{% content-ref url="api-reference.md" %}
[api-reference.md](api-reference.md)
{% endcontent-ref %}

### Contract addresses

{% content-ref url="../smart-contracts/contract-addresses.md" %}
[contract-addresses.md](../smart-contracts/contract-addresses.md)
{% endcontent-ref %}
