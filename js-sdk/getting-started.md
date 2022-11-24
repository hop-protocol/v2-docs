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

const hop = new Hop()
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
