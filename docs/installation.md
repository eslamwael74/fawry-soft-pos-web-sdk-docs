---
title: Installation
nav_order: 3
---

# Installation

---

## Install via npm

```bash
npm install fawry-softpos-sdk
```

After installing, build the SDK bundle:

```bash
cd node_modules/fawry-softpos-sdk
npm run build
```

This creates `dist/fawry-softpos-sdk.js` (UMD bundle).

---

## Install from Local Source

If you have the SDK source code locally (e.g., in a monorepo), reference it in your `package.json`:

```json
{
  "dependencies": {
    "fawry-softpos-sdk": "file:../js-sdk"
  }
}
```

Then install and build:

```bash
# Install dependencies (including the local SDK)
npm install

# Build the SDK
cd js-sdk
npm install
npm run build
```

---

## Loading the SDK

### Script Tag (recommended for most integrations)

Add the script to your HTML page:

```html
<script src="node_modules/fawry-softpos-sdk/dist/fawry-softpos-sdk.js"></script>
```

The SDK exposes a global `FawrySDK` object on `window`.

### ES Module / Bundler

If you use a bundler (Webpack, Vite, Rollup, etc.):

```javascript
import FawrySDK from 'fawry-softpos-sdk';
```

Or with CommonJS:

```javascript
const FawrySDK = require('fawry-softpos-sdk');
```

---

## Verify Installation

After loading the SDK, verify it is available:

```javascript
if (typeof FawrySDK !== 'undefined') {
    console.log('FawrySDK loaded successfully');
} else {
    console.error('FawrySDK failed to load. Check the script path.');
}
```

---

## Project Structure

After installation, your project should look like:

```
your-website/
├── node_modules/
│   └── fawry-softpos-sdk/
│       └── dist/
│           └── fawry-softpos-sdk.js   ← load this file
├── index.html                         ← your payment page
├── callback.html                      ← handles SoftPOS redirect
├── package.json
└── server.js                          ← your backend for signatures
```

---

## HTTPS Requirement

The SoftPOS app redirects back to your website after processing a payment. This callback URL **must be HTTPS** in production.

For local development, use [ngrok](https://ngrok.com) to create an HTTPS tunnel:

```bash
# Terminal 1: Start your server
npm start

# Terminal 2: Create HTTPS tunnel
ngrok http 8080
```

Use the `https://xxxx.ngrok.io` URL when testing on your Android device.
