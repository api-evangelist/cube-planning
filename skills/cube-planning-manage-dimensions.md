---
name: Manage Cube dimensions
description: Create, retrieve, and update planning dimensions in Cube, then recalculate dependent formulas.
api: openapi/cube-planning-openapi-original.yml
operations: [DimensionList, DimensionCreate, DimensionRetrieve, DimensionUpdate, dimensions_duplicate_create, formulas_recalculate_create]
---

# Manage Cube dimensions

Operating instructions for an agent working with dimensions in Cube's FP&A platform.

## Auth & headers (every request)
- Authenticate with OAuth 2.0 authorization-code + PKCE. Send the Bearer access token in `Authorization: Bearer <token>`. Scopes: `read`, `write`.
- Send the tenant header `X-Company-ID: <company id>` — required by most operations.
- Send the version header `Accept: application/json; version=1.0`.

## Steps
1. **List existing dimensions** — `GET /dimensions` (`DimensionList`) to see what already exists before creating duplicates.
2. **Create a dimension** — `POST /dimensions` (`DimensionCreate`). This is a `write`; confirm intent before running.
3. **Read one back** — `GET /dimensions/{dimension_id}` (`DimensionRetrieve`) to confirm the created id and shape.
4. **Update** — `POST /dimensions/{dimension_id}` (`DimensionUpdate`) to change a dimension.
5. **Duplicate when branching a scenario** — `POST /dimensions/{dimension_id}/duplicate` (`dimensions_duplicate_create`).
6. **Recalculate affected formulas** — `POST /formulas/{formula_id}/recalculate` (`formulas_recalculate_create`) after changing dimension values that feed formulas.

## Rules
- Responses are wrapped in `{ data, metadata }`; on error read `metadata.message` and the `errors[]` array (see errors/cube-planning-problem-types.yml).
- No idempotency-key is supported — do not blindly retry a `POST` that may have partially succeeded; re-list and reconcile first.
- Pagination on list endpoints uses `page` and `limit`.
