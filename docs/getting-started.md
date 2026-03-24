---
title: Getting Started
nav_order: 2
---

# Getting Started

This guide walks you through the minimal steps to integrate the Fawry SoftPOS Web SDK and process your first card payment.

---

## Prerequisites

Before you begin, make sure you have:

1. **Fawry merchant credentials** -- `merchantAccountNumber`, `partnerCode`, and `merchantToken`
2. **Android device** with the Fawry SoftPOS app installed
3. **HTTPS-enabled website** (use [ngrok](https://ngrok.com) for local development)
4. **Node.js 16+** (for the backend signature server)

---

## Step 1: Install the SDK

Add the SDK to your project:

```bash
npm install fawry-softpos-sdk
```

Or reference it locally:

```bash
npm install file:../js-sdk
```

Then build the SDK (if using the local source):

```bash
cd js-sdk
npm install
npm run build
```

---

## Step 2: Set Up Your Backend

The SDK requires a **server-generated signature** for every payment request. Never generate signatures on the client side.

Create a backend endpoint that calls the signature generation function:

```javascript
const { generatePaymentSignature } = require('./lib/generate-payment-signature');

// POST /api/generate-signature
app.post('/api/generate-signature', (req, res) => {
    const { amount, merchantAccountNumber, orderId, sid, clientTimeStamp } = req.body;

    const result = generatePaymentSignature({
        amount: String(amount),
        merchantAccountNumber,
        orderId,
        sid,
        clientTimeStamp,
    });

    res.json({ signature: result.signature });
});
```

See [Backend Setup]({% link backend-setup.md %}) for the full server implementation.

---

## Step 3: Load the SDK in Your Page

```html
<script src="node_modules/fawry-softpos-sdk/dist/fawry-softpos-sdk.js"></script>
```

Verify it loaded:

```javascript
if (typeof FawrySDK !== 'undefined') {
    console.log('SDK loaded successfully');
}
```

---

## Step 4: Make a Payment

```javascript
// 1. Generate a session ID
var sid = FawrySDK.generateSessionId();
var clientTimeStamp = Date.now();

// 2. Get a signature from your backend
var response = await fetch('/api/generate-signature', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
        amount: '100.00',
        merchantAccountNumber: 'YOUR_ACCOUNT_NUMBER',
        orderId: 'ORDER-001',
        sid: sid,
        clientTimeStamp: clientTimeStamp,
    }),
});
var data = await response.json();

// 3. Build and send the payment request
var result = await FawrySDK.requestSale(FawrySDK.PaymentOptionType.CARD)
    .setAmount(100.00)
    .setCurrency('EGP')
    .setSignature(data.signature)
    .setSid(sid)
    .setClientTimeStamp(clientTimeStamp)
    .setPartnerCode('YOUR_PARTNER_CODE')
    .setMerchantAccountNumber('YOUR_ACCOUNT_NUMBER')
    .setOrderId('ORDER-001')
    .send();

// 4. Handle the result
if (result.isSuccess()) {
    console.log('Payment successful!', result.body.fcrn);
} else {
    console.log('Payment failed:', result.header.status.statusDesc);
}
```

---

## Step 5: Set Up the Callback Page

Create a `callback.html` page that loads the SDK. The SDK **automatically** handles the callback when the URL contains a `sid` parameter:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Payment Result</title>
</head>
<body>
    <div id="result">Processing payment result...</div>
    <script src="node_modules/fawry-softpos-sdk/dist/fawry-softpos-sdk.js"></script>
    <script>
        FawrySDK.handleCallback().then(function(result) {
            var el = document.getElementById('result');
            if (result.isSuccess()) {
                el.textContent = 'Payment successful! FCRN: ' + result.body.fcrn;
            } else {
                el.textContent = 'Payment failed: ' + result.header.status.statusDesc;
            }
        });
    </script>
</body>
</html>
```

---

## Next Steps

- Read the [API Reference]({% link api-reference.md %}) for the full list of builder methods
- See [Card Sale]({% link operations/sale.md %}) for all sale-specific options (tips, promo codes, installments)
- Set up [Card Refund]({% link operations/refund.md %}) and [Card Void]({% link operations/void.md %}) flows
- Review the [Troubleshooting]({% link troubleshooting.md %}) guide for common issues
