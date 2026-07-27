---
name: Track training completion
description: Read training campaigns and each learner's campaign/module progress in Ethena.
api: openapi/goethena-openapi-original.yml
operations: [getTrainingCampaigns, getLearnerTrainingCampaigns, getLearnerTrainingCampaignById, getLearnerTrainingModules, getLearnerTrainingModuleById]
---

# Track training completion (Ethena API)

Report on who has completed which training in Ethena (`https://api.goethena.com`).

## Auth
- HTTP Basic (`Authorization: Basic base64(username:apiKey)`), sales-provisioned key.

## Steps
1. **List training campaigns** — `getTrainingCampaigns` (`GET /v1/training-campaigns`); each carries a status field. Read one with `getTrainingCampaignById` (`GET /v1/training-campaigns/{id}`).
2. **List learner campaign enrollments** — `getLearnerTrainingCampaigns` (`GET /v1/learner-training-campaigns`). Each item has `learnerId`, `trainingCampaignId`, `enrollmentStatus`, `completionStatus`, `nextTrainingAt`, `deadline`. Filter with `learnerStatus` / `trainingCampaignStatus`. Read one with `getLearnerTrainingCampaignById`.
3. **Drill into modules** — `getLearnerTrainingModules` (`GET /v1/learner-training-modules`) for per-module `status`, `moduleName`, `lengthInMinutes`, `assignedAt`, `statusUpdatedAt`. Read one with `getLearnerTrainingModuleById`.

## Rules
- All list endpoints are cursor-paginated (`cursor` + `limit`, `hasMore`) — loop until `hasMore` is false.
- Join module/campaign rows back to learners via `learnerId` and to campaigns via `trainingCampaignId` (see `data-model/goethena-data-model.yml`).
- For push-based completion, subscribe to the `LEARNER_TRAINING_CAMPAIGN_COMPLETED` webhook instead of polling (see the manage-webhooks skill).
