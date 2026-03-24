---
title: Card Refund
parent: Operations
nav_order: 2
---

# Card Refund

Refund a previously completed card transaction.

---

## Example

```javascript
var sid = FawrySDK.generateSessionId();
var clientTimeStamp = Date.now();

// Get signature from your backend (amount is required for refund signature)
var sigResponse = await fetch('/api/generate-signature', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
        amount: '150.00',
        merchantAccountNumber: 'YOUR_ACCOUNT_NUMBER',
        sid: sid,
        clientTimeStamp: clientTimeStamp,
    }),
});
var sigData = await sigResponse.json();

try {
    var result = await FawrySDK.requestRefund(FawrySDK.PaymentOptionType.CARD)
        .setAmount(150.00)
        .setTransactionFCRN('ORIGINAL_FCRN_HERE')
        .setSignature(sigData.signature)
        .setSid(sid)
        .setClientTimeStamp(clientTimeStamp)
        .setPartnerCode('YOUR_PARTNER_CODE')
        .setMerchantAccountNumber('YOUR_ACCOUNT_NUMBER')
        .send();

    if (result.isSuccess()) {
        console.log('Refund successful! FCRN:', result.body.fcrn);
    }
} catch (error) {
    console.error('Refund failed:', error.message);
}
```

---

## Parameters

| Parameter | Method | Required | Description |
|-----------|--------|----------|-------------|
| Amount | `setAmount()` | **Yes** | Refund amount |
| Transaction FCRN | `setTransactionFCRN()` | No | FCRN of the original transaction |
| Order ID | `setOrderId()` | No | Order/reference ID |
| Split Payment | `setSplitPayment()` | No | Enable split payment refund |
| Chain UID | `setChainUID()` | No | Chain UID for split payments |

Plus all [common builder methods]({% link api-reference.md %}#common-methods-all-builders) (signature, sid, timestamp, etc.).

---

## Notes

- The refund amount should match or be less than the original transaction amount.
- Use the `transactionFCRN` from the original sale's `result.body.fcrn` to link the refund to the correct transaction.
