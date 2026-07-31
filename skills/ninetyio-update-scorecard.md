---
name: Update Ninety Scorecard
description: Query Scorecard Measurables (KPIs) and record weekly scores and notes via the Ninety Public API.
api: openapi/ninetyio-openapi-original.json
operations: [PublicScorecardController_queryKpis, PublicScorecardController_putScore, PublicScorecardController_putNote, PublicScorecardController_deleteScore, PublicScorecardController_deleteNote]
---

# Update Ninety Scorecard

Keep a team's EOS Scorecard current by writing Measurable (KPI) scores and notes.

## Auth & base
- Base URL: `https://api.public.ninety.io/v1`
- `Authorization: Bearer <PAT>` on every call.

## Steps
1. List Measurables: `POST /v1/scorecard/kpis/query` (`PublicScorecardController_queryKpis`) filtered by `teamId`; read `items` for each `kpiId`.
2. Record a score for a period: `POST /v1/scorecard/kpis/{kpiId}/scores` (`PublicScorecardController_putScore`) with the value and `periodStartDate`. This is an upsert (create-or-update).
3. Attach a note: `POST /v1/scorecard/kpis/{kpiId}/notes` (`PublicScorecardController_putNote`) with the note text and `periodStartDate` (also an upsert).
4. Correct mistakes: `DELETE /v1/scorecard/kpis/{kpiId}/scores/{periodStartDate}` (`PublicScorecardController_deleteScore`) or `.../notes/{periodStartDate}` (`PublicScorecardController_deleteNote`).

## Rules
- `putScore`/`putNote` are idempotent by `(kpiId, periodStartDate)` — re-posting the same period overwrites rather than duplicates.
- Handle `429` with a one-second backoff (25 req/s limit).
- Errors return `{ message, error, statusCode }`.
