---
name: Manage learners
description: Create, read, update, and deactivate learners in Ethena via the REST API.
api: openapi/goethena-openapi-original.yml
operations: [getLearners, createLearner, getLearnerById, patchLearnerById, deleteLearnerById]
---

# Manage learners (Ethena API)

Use the Ethena API (`https://api.goethena.com`) to keep your learner roster in sync with Ethena.

## Auth
- HTTP Basic. Send `Authorization: Basic base64(username:apiKey)`. API keys are provisioned by your Ethena sales representative.
- A missing/invalid key returns `401`.

## Steps
1. **List learners** — `getLearners` (`GET /v1/learners`). Filter with `status`, `email`, `name`, `id`. Page with `cursor` + `limit`; the response envelope is `{ limit, hasMore, data[] }` — keep requesting with the next `cursor` while `hasMore` is true.
2. **Create a learner** — `createLearner` (`POST /v1/learners`) with the learner's `name`, `email`, `country`, `language`, `isManager`. A `400` returns RFC 7807 `{ title, detail, invalid-params[] }` on validation failure.
3. **Read one** — `getLearnerById` (`GET /v1/learners/{id}`). `404` if the learner id does not exist.
4. **Update** — `patchLearnerById` (`PATCH /v1/learners/{id}`) to change fields such as `status` (`ACTIVE`, `DEACTIVATED`, `TERMINATED`). Returns `204`.
5. **Remove** — `deleteLearnerById` (`DELETE /v1/learners/{id}`). Returns `204`.

## Rules
- No idempotency-key is supported; guard `createLearner` retries in your own code to avoid duplicates.
- Deactivated/terminated learners cannot access training — patch `status` rather than deleting when you only need to revoke access.
- Errors follow `errors/goethena-problem-types.yml`; conventions in `conventions/goethena-conventions.yml`.
