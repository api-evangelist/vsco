---
name: Subscribe to Workspace REST Hook events
description: Register, verify, and clean up webhook subscriptions for contact, job, order, and payment events.
api: openapi/workspace-openapi-original.json
generated: '2026-07-21'
method: generated
operations: [listResourceRestHook, createResourceRestHook, getResourceRestHook, updateResourceRestHook, deleteResourceRestHook]
---

# Subscribe to REST Hook events

Base URL: `https://workspace.vsco.co/api/v2`, auth header `X-API-KEY`. The webhook model
is REST Hooks (resthooks.org): you subscribe a target URL to one event per subscription.

1. **Audit existing subscriptions** — `GET /rest-hook` (`listResourceRestHook`) and skip
   creation if an identical `event` + `url` pair already exists.
2. **Subscribe** — `POST /rest-hook` (`createResourceRestHook`) with required `event`
   (one of `contact.created`, `job.created`, `order.booked`, `payment.created`) and a
   required HTTPS `url`. Narrow delivery with the optional `limitTo` object:
   `contactKinds` (contact.created only), `jobTypes` (not on contact.created), `brands`,
   `webLead` (job.created only).
3. **Verify** — `GET /rest-hook/{id}` (`getResourceRestHook`); adjust filters with
   `PUT /rest-hook/{id}` (`updateResourceRestHook`).
4. **Unsubscribe** — `DELETE /rest-hook/{id}` (`deleteResourceRestHook`) when the
   receiver is retired; a `204` confirms deletion.

Rules: one event per subscription — register four subscriptions to cover all events.
Receivers should respond fast and process asynchronously; dedupe deliveries by resource
id since there is no signature/replay documentation. `429` responses carry `Retry-After`.
