---
name: Register and manage event webhooks
description: Create, inspect, update, and delete Verifiable webhooks and verify their delivery.
api: openapi/verifiable-openapi-original.json
operations: [CreateWebhook, ListWebhooks, GetWebhook, PatchWebhook, DeleteWebhook, ListWebhooksLog]
auth: bearer-token
base_url: https://discovery.verifiable.com/api
---

# Register and manage event webhooks

Use this skill to receive Verifiable events (e.g. verification results, monitor
alerts) as outbound HTTP callbacks instead of polling.

## Rules
- Bearer access token in `Authorization` on every management call.
- Verifiable POSTs event payloads to your URL. The event type is in the
  `X-WebhookType` header; the webhook id is in `X-WebhookId`; an optional shared
  secret is in `X-Secret`; and a stable per-message id is in `X-TraceId`.
- Use `X-TraceId` to de-duplicate: it does **not** change across the up-to-3
  retry attempts, so process each trace id at most once.
- Use HTTPS callback URLs. Insecure URLs are allowed only for testing.
- Return a 2xx status quickly; failed deliveries are retried a maximum of 3 times.

## Steps
1. **Register** — `CreateWebhook` (`POST /webhooks`) with your callback URL, the
   event type to subscribe to, and (recommended) a secret.
2. **List** — `ListWebhooks` (`GET /webhooks`) to see all webhooks on the
   organization.
3. **Inspect** — `GetWebhook` (`GET /webhooks/{webhookId}`) for one webhook's config.
4. **Update** — `PatchWebhook` (`PATCH /webhooks/{webhookId}`) to change the URL,
   secret, or subscribed type.
5. **Verify delivery** — `ListWebhooksLog` (`GET /webhookslog`) to audit recent
   delivery attempts and outcomes.
6. **Remove** — `DeleteWebhook` (`DELETE /webhooks/{webhookId}`) when no longer needed.

## Tips
- Register multiple webhook types against one endpoint and branch on
  `X-WebhookType`. See `asyncapi/verifiable-webhooks.yml` and the API's
  Webhook Callbacks reference for payload models.
