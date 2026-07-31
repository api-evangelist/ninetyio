---
name: Manage Ninety Issues
description: Log, query, update, and resolve EOS Issues on a team's Issues List via the Ninety Public API.
api: openapi/ninetyio-openapi-original.json
operations: [PublicTeamController_findAll, PublicIssueController_queryIssues, PublicIssueController_createIssue, PublicIssueController_getIssue, PublicIssueController_updateIssue, PublicIssueController_deleteIssue]
---

# Manage Ninety Issues

Operate a team's EOS Issues List (IDS: Identify, Discuss, Solve) through the Ninety Public API.

## Auth & base
- Base URL: `https://api.public.ninety.io/v1`
- Send a Personal Access Token as `Authorization: Bearer <PAT>` on every call. Generate at https://app.ninety.io/settings/user/developer-settings. Calls run as your user and honor your in-app permissions.

## Steps
1. Resolve the team: `GET /v1/teams` (`PublicTeamController_findAll`) and pick the `teamId`.
2. Review open issues: `POST /v1/issues/query` (`PublicIssueController_queryIssues`) with a JSON body — filter by `teamId`, set `completed: false`, and page with `page`/`pageSize` (max 100). Read `items` and `totalCount` from the response envelope.
3. Log a new issue: `POST /v1/issues` (`PublicIssueController_createIssue`) with `title`, optional `description`, `teamId`, `priority`.
4. Inspect one: `GET /v1/issues/{issueId}` (`PublicIssueController_getIssue`).
5. Solve/close: `PATCH /v1/issues/{issueId}` (`PublicIssueController_updateIssue`) setting `completed: true` (and any edits).
6. Remove: `DELETE /v1/issues/{issueId}` (`PublicIssueController_deleteIssue`).

## Rules
- No idempotency key exists — do not blindly retry `POST /v1/issues` on a timeout; re-query first to avoid duplicates.
- Respect the 25 req/s limit; on `429` back off one second and retry.
- Errors return `{ message, error, statusCode }` (NestJS envelope), not RFC 9457.
