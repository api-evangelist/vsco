---
name: Create a contact and open a job in VSCO Workspace
description: Add a person to the studio address book, open a new job (lead), and link the contact to the job with a role.
api: openapi/workspace-openapi-original.json
generated: '2026-07-21'
method: generated
operations: [listResourceAddressBook, createResourceAddressBook, createResourceJob, createResourceJobContact, getResourceJob]
---

# Create a contact and open a job

Base URL: `https://workspace.vsco.co/api/v2` — every request needs the API key in the
`X-API-KEY` header (created in Workspace Settings > API Integrations; use a Read and
Write key for this flow).

1. **Dedupe first** — `GET /address-book` (`listResourceAddressBook`) with the `email`
   query parameter to check whether the contact already exists. Collections page with
   `page` / `pageSize` (max 100).
2. **Create the contact** — `POST /address-book` (`createResourceAddressBook`) with the
   person's name, email, and address. Contact kinds are `person`, `company`, or
   `location`.
3. **Open the job** — `POST /job` (`createResourceJob`). Set `jobTypeId`, `brandId`,
   `leadSourceId`, and `leadStatusId` from the studio's configured lookup lists (list
   them via their own endpoints, e.g. `GET /job-type`).
4. **Link contact to job** — `POST /job-contact` (`createResourceJobContact`) with the
   new `contactId`, `jobId`, and the contact's role on the job.
5. **Confirm** — `GET /job/{id}` (`getResourceJob`) and follow the resource's `links`
   object.

Rules: on `400` the envelope is `{type: validation-error, title, detail, status}` — fix
the fields named in the detail breakdown. On `429` (`quota-limit`) honor the
`Retry-After` header. There is no idempotency key — dedupe by `email` /
`externalMappings` before creating, never by blind retry.
