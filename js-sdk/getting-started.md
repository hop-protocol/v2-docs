---
description: ⚠️ JavaScript SDK is in beta
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

const hop = new Hop('goerli')
```

Avaiable networks are only `goerli` at this time.

## Send Message

> Send a message from one chain to another chain.

Use this method to get the populated transaction for sending a message.

Method: `getSendMessagePopulatedTx`

Parameters:

|Name|Type|Description|Example|
|----|----|-----------|-------|
|`fromChainId`|`number`|From chain ID|eg. `420`|
|`toChainId`|`number`|To chain ID|eg. `5`|
|`toAddress`|`string`|The address to call at the destination|eg. `0x5f335A890bbB5Cd1a4a7f7A1C8F9F6F75efDCA89`|
|`toCalldata`|`string`|The tx calldata to call at the destination address|eg. `0x812448a5000000000000000000000000000000000000000000000000000000000000002a`|

Example

```ts
import { Hop } from '@hop-protocol/v2-sdk'

async function main() {
  const fromChainId = 420
  const toChainId = 5
  const toAddress = "0x5f335A890bbB5Cd1a4a7f7A1C8F9F6F75efDCA89"
  const toCalldata = "0x812448a5000000000000000000000000000000000000000000000000000000000000002a"

  const hop = new Hop('goerli')
  const txData = await hop.getSendMessagePopulatedTx({
    fromChainId,
    toChainId,
    toAddress,
    toCalldata
  })
  console.log(txData)
}

main().catch(console.error)
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
