---
name: Export Cube data to a destination
description: Analyze a Cube matrix and run a data export, then track exports and scheduled BI exports.
api: openapi/cube-planning-openapi-original.yml
operations: [cube_analyze_create, cube_export_create, cube_export_list, cube_export_schedule_list, cube_export_notification_stale_data_retrieve]
---

# Export Cube data to a destination

Operating instructions for an agent extracting planning data out of Cube.

## Auth & headers (every request)
- OAuth 2.0 Bearer token in `Authorization`; scopes `read` (list/analyze) and `write` (begin export).
- Required tenant header `X-Company-ID: <company id>`.
- Version header `Accept: application/json; version=1.0`.

## Steps
1. **Analyze the matrix** — `POST /cube/analyze` (`cube_analyze_create`) to resolve the dimensions/shape you intend to export.
2. **Begin an export** — `POST /cube/export` (`cube_export_create`). This is a `write` operation that kicks off a job.
3. **Poll export status** — `GET /cube/export` (`cube_export_list`) to watch the job move to completion.
4. **Check for stale-data notifications** — `GET /cube/export/notification/stale-data` (`cube_export_notification_stale_data_retrieve`) so you do not export against stale source data.
5. **Inspect scheduled BI exports** — `GET /cube/export/schedule` (`cube_export_schedule_list`) when the export should recur.

## Rules
- Exports are asynchronous — treat `cube_export_create` as starting a job and confirm completion via `cube_export_list` before consuming output.
- Responses use the `{ data, metadata }` envelope; errors surface in `metadata.message` + `errors[]`.
- A `409` on cube operations means the cube has active work (publish lock, import blocks, or maintenance lock) — back off and retry later.
