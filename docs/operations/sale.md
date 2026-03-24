---
title: Card Sale
parent: Operations
nav_order: 1
---

# Card Sale

Accept a card payment through the SoftPOS app.

---

## Basic Example

```javascript
// 1. Generate session ID and timestamp
var sid = FawrySDK.generateSessionId();
var clientTimeStamp = Date.now();

// 2. Get signature from your backend
var sigResponse = await fetch('/api/generate-signature', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
        amount: '150.00',
        merchantAccountNumber: 'YOUR_ACCOUNT_NUMBER',
        orderId: 'ORD-12345',
        sid: sid,
        clientTimeStamp: clientTimeStamp,
    }),
});
var sigData = await sigResponse.json();

// 3. Build and send payment request
try {
    var result = await FawrySDK.requestSale(FawrySDK.PaymentOptionType.CARD)
        .setAmount(150.00)
        .setCurrency('EGP')
        .setSignature(sigData.signature)
        .setSid(sid)
        .setClientTimeStamp(clientTimeStamp)
        .setPartnerCode('YOUR_PARTNER_CODE')
        .setMerchantAccountNumber('YOUR_ACCOUNT_NUMBER')
        .setOrderId('ORD-12345')
        .send();

    // Payment successful
    console.log('FCRN:', result.body.fcrn);
    console.log('Amount:', result.body.amount);
} catch (error) {
    // Payment failed or timed out
    console.error('Payment error:', error.message);
}
```

---

## Full Example with All Options

```javascript
var result = await FawrySDK.requestSale(FawrySDK.PaymentOptionType.CARD)
    // Required
    .setAmount(250.00)
    .setSignature(signatureFromBackend)
    .setSid(sid)
    .setClientTimeStamp(clientTimeStamp)
    .setPartnerCode('100')
    .setMerchantAccountNumber('ACCT-001')

    // Optional
    .setCurrency('EGP')
    .setOrderId('ORD-67890')
    .setTips(10.00)
    .setPromoCode('PROMO2024')
    .setReceiptNumber('REC-001')
    .setSplitPayment(false)
    .setPrintReceipt(true)
    .setDisplayInvoice(true)
    .setMetadata({ customField: 'value' })
    .setExtras({ note: 'VIP customer' })
    .send();
```

---

## Required Parameters

| Parameter | Method | Description |
|-----------|--------|-------------|
| Amount | `setAmount()` | Payment amount (number) |
| Signature | `setSignature()` | Server-generated signature |
| Session ID | `setSid()` | From `FawrySDK.generateSessionId()` |
| Timestamp | `setClientTimeStamp()` | `Date.now()` |
| Partner Code | `setPartnerCode()` | Your Fawry partner code |
| Account Number | `setMerchantAccountNumber()` | Your merchant account number |

---

## Handling the Result

```javascript
if (result.isSuccess()) {
    // statusCode === 1
    var fcrn = result.body.fcrn;                    // Transaction reference
    var amount = result.body.amount;                // Charged amount
    var cardInfo = result.body.receiptInfo.cardInfo; // Card details
    var maskedPan = cardInfo.primaryAccountNumber;   // e.g. "****1234"
    var authId = result.body.receiptInfo.authId;     // Authorization ID
} else {
    var errorMsg = result.header.status.hostStatusDesc;
    var statusCode = result.header.status.statusCode;
}
```

---

## Timeout

The SDK waits up to **3 minutes** for a response from the SoftPOS app. If no response is received, the Promise rejects with:

```
Payment timeout: No response received within 3 minutes
```
