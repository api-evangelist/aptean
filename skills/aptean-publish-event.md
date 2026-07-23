---
name: Publish an event on the Aptean Integration Platform
description: Register an event definition for a product and publish an event instance from a tenant so subscribed consumers receive it.
api: openapi/aptean-integration-platform-openapi-original.json
operations:
- POST /v1/event-definitions
- GET /v1/event-definitions
- POST /v1/events
- GET /v1/events/{eventId}
---

# Publish an event on the Aptean Integration Platform (AIP)

Use this skill to define an event type for a product and publish event
instances that AIP delivers to subscribed consumers.

## Authentication
Send on every request (see `authentication/aptean-authentication.yml` and
`conventions/aptean-conventions.yml`):
- `Authorization: Bearer {JWT}`
- `X-APTEAN-TENANT: {tenantId}`
- `X-APTEAN-APIM: {apimKey}`
- `X-APTEAN-PRODUCT: {productId}`
- Optionally `X-APTEAN-CORRELATION-ID: {id}` to correlate this publish with
  downstream consumer logs.

## Steps
1. **Define the event type** — `POST /v1/event-definitions` with the event
   name, type, productId, productVersion, and a `payloadSchema`. Confirm it is
   registered with `GET /v1/event-definitions` (filter by productId).
2. **Publish an event** — `POST /v1/events` with the `eventDefinitionId` and a
   `payload` that validates against the definition's `payloadSchema`. Include a
   `correlationId` for traceability.
3. **Verify** — `GET /v1/events/{eventId}` to confirm the event was accepted
   (check `sentTimestamp`; a `failedTimestamp`/`failureMessage` indicates a
   delivery problem).

## Error handling
Errors return an `AipErrorResponse` envelope (`errors[]`/`warnings[]`, each an
RFC 7807 ProblemDetails). Handle `400` (payload fails schema validation),
`401` (missing/invalid token or tenant/product/APIM headers), and `500`. See
`errors/aptean-problem-types.yml`.
