---
name: Manage Ninety To-Dos
description: Create, query, update, and complete EOS To-Dos via the Ninety Public API.
api: openapi/ninetyio-openapi-original.json
operations: [PublicTeamController_findAll, PublicTodoController_queryTodos, PublicTodoController_create, PublicTodoController_findOne, PublicTodoController_update, PublicTodoController_delete]
---

# Manage Ninety To-Dos

Track short-term commitments on a team's EOS To-Do list.

## Auth & base
- Base URL: `https://api.public.ninety.io/v1`
- `Authorization: Bearer <PAT>` on every call.

## Steps
1. Resolve the team: `GET /v1/teams` (`PublicTeamController_findAll`).
2. Create a To-Do: `POST /v1/todos` (`PublicTodoController_create`) with `title`, optional `description`, `dueDate`, `teamId`, `userId`, or `isPersonal: true` for a personal item.
3. List open items: `POST /v1/todos/query` (`PublicTodoController_queryTodos`) filtered by `teamId`, `completed: false`, paging via `page`/`pageSize`.
4. Inspect: `GET /v1/todos/{id}` (`PublicTodoController_findOne`).
5. Complete/edit: `PATCH /v1/todos/{id}` (`PublicTodoController_update`) with `completed: true`.
6. Remove: `DELETE /v1/todos/{id}` (`PublicTodoController_delete`).

## Rules
- No idempotency key — re-query before retrying a create to avoid duplicates.
- Handle `429` with a one-second backoff (25 req/s limit).
- Errors return `{ message, error, statusCode }`.
