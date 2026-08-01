---
name: Record a payment and apply it to an order
description: Find a job's booked order, record a payment against the job, and allocate it to the order.
api: openapi/workspace-openapi-original.json
generated: '2026-07-21'
method: generated
operations: [listResourceJobOrders, getResourceOrder, createResourcePayment, createResourceApplyPaymentToOrder, getResourcePayment]
---

# Record and apply a payment

Base URL: `https://workspace.vsco.co/api/v2`, auth header `X-API-KEY` (Read and Write
key required).

1. **Find the order** — `GET /job/{jobId}/order` (`listResourceJobOrders`) for the job,
   or `GET /order/{id}` (`getResourceOrder`) when you already hold the order id.
2. **Record the payment** — `POST /payment` (`createResourcePayment`) with `jobId`,
   `payerId` (the paying contact), `paymentMethodId`, `amount`, and `received` date.
   List valid methods with `GET /payment-method`.
3. **Allocate to the order** — `POST /payment/{id}/apply`
   (`createResourceApplyPaymentToOrder`) to apply the recorded payment to the order's
   open balance.
4. **Verify** — `GET /payment/{id}` (`getResourcePayment`); refunds appear as nested
   `refunds[]` payments and allocations as `paymentAllocations[]`.

Rules: amounts are positive money values; there is no idempotency header, so before any
retry of step 2 list payments (`GET /payment` filtered by `jobId`) to make sure the
payment was not already created. `429` responses carry `Retry-After`; validation
failures return the `validation-error` envelope with a field-level `detail` object.
A `payment.created` REST Hook fires on success — do not double-process in listeners.
