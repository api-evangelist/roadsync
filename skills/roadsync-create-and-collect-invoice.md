---
name: Create and collect an invoice
description: Create a RoadSync invoice, send a pay link to the payer, and refund it if needed.
api: openapi/roadsync-invoice-openapi.json
operations: [create-invoice, send-invoice, get-invoice, refund-invoice, void-invoice]
---

# Create and collect an invoice (RoadSync Invoice API)

Base URL: `https://api.roadsync.app/v1/invoice` (test: `https://test.api.roadsync.app/v1/invoice`).
Auth: send your key in the `x-api-key` header on every request.

## Steps

1. **Create the invoice** — `POST /` (`create-invoice`) with line items, payer contact
   details, and any custom fields. Capture the returned invoice `id`.
2. **Send the pay link** — `POST /{id}/send` (`send-invoice`) to email the payer a URL
   where they can view the invoice and pay (fuel card, fleet check, or card).
3. **Check status** — `GET /{id}` (`get-invoice`) to poll payment status.
4. **Refund if required** — `POST /{id}/refund` (`refund-invoice`) to refund a paid
   invoice; use `POST /{id}/void` (`void-invoice`) to void one that is unpaid.

## Rules

- Errors: non-2xx responses carry an `application/json` body (the legacy Client API uses
  RFC 9457 `application/problem+json`). See `errors/roadsync-problem-types.yml`.
- Test against `test.api.roadsync.app` with a test key before going live.
- Never hard-code keys; treat the `x-api-key` value as a full-access secret.
