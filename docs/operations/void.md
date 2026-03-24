---
title: Card Void
parent: Operations
nav_order: 3
---

# Card Void

Void (cancel) a recently completed card transaction. Voids are typically available only within the same batch/settlement period.

---

## Example

```javascript
var sid = FawrySDK.generateSessionId();
var clientTimeStamp = Date.now();

// Get signature from your backend (no amount needed for void)
var sigResponse = await fetch('/api/generate-signature', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
        merchantAccountNumber: 'YOUR_ACCOUNT_NUMBER',
        sid: sid,
        clientTimeStamp: clientTimeStamp,
    }),
});
var sigData = await sigResponse.json();

try {
    var result = await FawrySDK.requestVoid(FawrySDK.PaymentOptionType.CARD)
        .setTransactionFCRN('ORIGINAL_FCRN_HERE')
        .setSignature(sigData.signature)
        .setSid(sid)
        .setClientTimeStamp(clientTimeStamp)
        .setPartnerCode('YOUR_PARTNER_CODE')
        .setMerchantAccountNumber('YOUR_ACCOUNT_NUMBER')
        .send();

    if (result.isSuccess()) {
        console.log('Void successful!');
    }
} catch (error) {
    console.error('Void failed:', error.message);
}
```

---

## Parameters

| Parameter | Method | Required | Description |
|-----------|--------|----------|-------------|
| Transaction FCRN | `setTransactionFCRN()` | No | FCRN of the transaction to void |
| Order ID | `setOrderId()` | No | Order/reference ID |
| Split Payment | `setSplitPayment()` | No | Enable split payment void |
| Chain UID | `setChainUID()` | No | Chain UID for split payments |

Plus all [common builder methods]({% link api-reference.md %}#common-methods-all-builders) (signature, sid, timestamp, etc.).

---

## Successful Void

A successful void returns `statusCode: 3` (REVERSED), which `result.isSuccess()` recognizes as success.

```javascript
result.isSuccess()                    // true when statusCode is 1 or 3
result.header.status.statusCode       // 3 (REVERSED)
result.header.status.statusDesc       // 'reversed'
```
