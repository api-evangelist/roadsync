---
name: Disburse a payment to a carrier
description: Register a payee and funding source, then create an idempotent RoadSyncPay payable and transaction.
api: openapi/roadsync-rspay-openapi.json
operations: [post-payees, get-payees-id, get-funding-sources-id, post-payables, post-transactions, get-transactions-id]
---

# Disburse a carrier payment (RoadSyncPay Public API)

Base URL: `https://api.roadsync.app/rspay/v1` (test: `https://test.api.roadsync.app/rspay/v1`).
Auth: `x-api-key` header on every request.

## Steps

1. **Create or find the payee** — `POST /payees` (`post-payees`) for a new carrier or
   factoring company, or `GET /payees/{payee_id}` (`get-payees-id`) to look one up.
2. **Get the funding source** — `GET /payees/{payee_id}/funding-sources`
   (`get-funding-sources-id`) to choose an ACH / paper-check / RTP destination.
3. **Create the payable** — `POST /payables` (`post-payables`). Include an
   `idempotency_key` so a retry cannot create a duplicate disbursement.
4. **Create the transaction** — `POST /transactions` (`post-transactions`), again with an
   `idempotency_key`, to move the money.
5. **Confirm** — `GET /transactions/{id}` (`get-transactions-id`) to read final status.

## Rules

- **Idempotency is first-class**: payables and transactions carry an `idempotency_key`
  string (`components.schemas.idempotencyKey`). Always set it on writes and reuse the same
  value when retrying. See `conventions/roadsync-conventions.yml`.
- Errors return `application/json` with 400/401/403/404/409 status codes; 409 signals a
  conflict (e.g. a duplicate idempotency_key). See `errors/roadsync-problem-types.yml`.
- Exercise the flow on `test.api.roadsync.app` first.
