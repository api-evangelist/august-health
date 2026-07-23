---
name: Record a batch of collected payments as a deposit
description: Authenticate with billing permission and submit collected resident payments as a single idempotent, atomic deposit.
api: developer.augusthealth.com
operations: [createTokenV1, listFacilityBillingSummariesV1, recordDepositV1]
---

# Record a deposit in August Health

Grounded in the [Record deposit reference](https://developer.augusthealth.com/reference/recorddepositv1.md).

## Auth
1. `createTokenV1` — obtain the `idToken`. The API user must hold the `BILLING_PAYMENTS_CREATE` permission or the call returns 403.

## Prepare
2. `listFacilityBillingSummariesV1` — resolve residents (`personId`) and current balances/payer contacts before building the batch.

## Submit
3. `recordDepositV1` — submit collected payments as one consolidated deposit.
   - Set a stable `externalDepositId` and a stable `externalTransactionId` per payment.
   - `paymentType`: `PAYMENT` (positive), `PAYMENT_RETURN` (negative, e.g. NSF), or `REFUND` (negative). Omitting it defaults to `PAYMENT`.
   - `cashAccountCode`: **required and must match a configured code** if the facility has GL cash accounts; **must be omitted** if none are configured.

## Rules (idempotency + atomicity)
- **Idempotent:** resubmitting the same `externalDepositId` returns the existing deposit — but the retry must carry the exact same set of payments, or you get `400`.
- **Atomic:** all payments succeed or none do; one invalid `personId` rejects the whole batch.
- Safe retry on network failure: re-send the identical batch with the same `externalDepositId`.
- 10 req/s ceiling; back off on `429`.
