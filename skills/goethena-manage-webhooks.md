---
name: Manage webhooks
description: Register and manage Ethena webhooks for training-campaign-completion events.
api: openapi/goethena-openapi-original.yml
operations: [getWebhooks, createWebhook, getWebhookById, patchWebhookById, deleteWebhookById, triggerWebhookById]
---

# Manage webhooks (Ethena API)

Subscribe to Ethena events so you are notified when learners complete training, instead of polling.

## Prerequisites
- Webhooks are **opt-in**: ask your Ethena sales representative to enable them for your account. Until then, webhook operations return `403`.
- Auth is HTTP Basic (`Authorization: Basic base64(username:apiKey)`).

## Steps
1. **Register a subscription** — `createWebhook` (`POST /v1/webhooks`) with `url` (your HTTPS callback, max 2048 chars) and `type` = `LEARNER_TRAINING_CAMPAIGN_COMPLETED` (the only supported type today).
2. **List / read** — `getWebhooks` (`GET /v1/webhooks`) and `getWebhookById` (`GET /v1/webhooks/{id}`).
3. **Update** — `patchWebhookById` (`PATCH /v1/webhooks/{id}`) to change the callback `url`. Returns `204`.
4. **Test delivery** — `triggerWebhookById` (`POST /v1/webhooks/{id}/trigger`) to fire a test event at your endpoint. Returns `204`.
5. **Remove** — `deleteWebhookById` (`DELETE /v1/webhooks/{id}`). Returns `204`.

## Event payload
`LEARNER_TRAINING_CAMPAIGN_COMPLETED` delivers `{ learnerId, trainingCampaignId, statusTimestamp }` (all required). Join back to learners/campaigns by those ids. See `asyncapi/goethena-events-asyncapi.yml`.

## Rules
- `403` means webhooks are not enabled for the account; `400` returns RFC 7807 `{ title, detail, invalid-params[] }`.
- Respond `2xx` quickly from your callback and process asynchronously.
