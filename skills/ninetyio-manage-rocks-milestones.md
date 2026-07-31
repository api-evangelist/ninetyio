---
name: Manage Ninety Rocks and Milestones
description: Create and track quarterly EOS Rocks (priorities) and their Milestones via the Ninety Public API.
api: openapi/ninetyio-openapi-original.json
operations: [PublicTeamController_findAll, PublicRockController_queryRocks, PublicRockController_create, PublicRockController_findOne, PublicRockController_update, PublicMilestoneController_create, PublicMilestoneController_update, PublicMilestoneController_findOne]
---

# Manage Ninety Rocks and Milestones

Set and track quarterly EOS Rocks and their supporting Milestones.

## Auth & base
- Base URL: `https://api.public.ninety.io/v1`
- `Authorization: Bearer <PAT>` on every call.

## Steps
1. Resolve the team: `GET /v1/teams` (`PublicTeamController_findAll`).
2. Create a Rock: `POST /v1/rocks` (`PublicRockController_create`) with `title`, `description`, `teamId`, `userId` (owner), `dueDate`, `quarter`, and `levelCode`/`statusCode` as needed.
3. Add Milestones to the Rock: `POST /v1/milestones` (`PublicMilestoneController_create`) linking each to the Rock's id, with a `title` and due date.
4. Track progress: `POST /v1/rocks/query` (`PublicRockController_queryRocks`) filtered by `teamId`/`quarter`/`statusCode`; read the Rock via `GET /v1/rocks/{id}` (`PublicRockController_findOne`) — it embeds `milestones`.
5. Update status: `PATCH /v1/rocks/{id}` (`PublicRockController_update`) to move `statusCode` or mark `completed`; `PATCH /v1/milestones/{id}` (`PublicMilestoneController_update`) to complete a milestone.

## Rules
- Rock ids are returned as `_id`; milestones live under the Rock's `milestones` array.
- No idempotency key — re-query before retrying a create.
- Handle `429` with a one-second backoff (25 req/s limit).
