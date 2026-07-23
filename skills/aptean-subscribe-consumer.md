---
name: Subscribe a consumer and receive Aptean events by webhook
description: Register a consumer subscription to one or more event definitions, then receive and verify signed webhook event deliveries.
api: openapi/aptean-integration-platform-openapi-original.json
operations:
- GET /v1/producers
- POST /v1/consumers
- GET /v1/consumers/{id}
- GET /v1/events/consumer-events/{consumerId}
- GET /v1/public-keys
---

# Subscribe a consumer and receive Aptean events by webhook

Use this skill to subscribe a tenant to a producer's events on the Aptean
Integration Platform (AIP) and receive them via signed webhook deliveries.

## Authentication
Send on every request (see `authentication/aptean-authentication.yml`):
- `Authorization: Bearer {JWT}`
- `X-APTEAN-TENANT`, `X-APTEAN-APIM`, `X-APTEAN-PRODUCT`

## Steps
1. **Find the producer** — `GET /v1/producers` (filter by productId) to locate
   the producer tenant whose events you want.
2. **Create the subscription** — `POST /v1/consumers` with `producerId`,
   `productId`, the `subscribedEvents` (event-definition references), your
   webhook inbox endpoint, and `contactEmail`. Confirm with
   `GET /v1/consumers/{id}` (uses operationId `GetConsumerById`).
3. **Receive webhooks** — AIP delivers each matching Event to your inbox via
   HTTPS POST. See `asyncapi/aptean-events-webhooks.yml`.
4. **Verify signatures** — fetch signing keys with `GET /v1/public-keys` and
   validate each webhook payload signature (the C# SignatureVerifier sample in
   the integration-services repo shows the flow).
5. **Reconcile** — `GET /v1/events/consumer-events/{consumerId}` to list the
   events published to your subscription for audit/backfill.

## Error handling
Errors return the `AipErrorResponse` envelope (RFC 7807 ProblemDetails items).
Handle `400`, `401`, and `404` (unknown producer/consumer). See
`errors/aptean-problem-types.yml`.
