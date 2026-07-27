---
name: Sync employee training completion
description: Read employee-training records and update completion status or rate to keep an external system of record in sync with Haekka.
api: openapi/haekka-public-api-openapi.yml
operations: [listEmployeeTrainings, getEmployeeTraining, updateEmployeeTraining]
---

# Sync employee training completion

Use the Haekka Public API to read training progress and push completion updates (for example, from an LMS or HRIS reconciliation job).

## Authentication
Bearer token in the `Authorization` header. Base URL: `https://app.haekka.com/api/public`.

## Steps

1. **Enumerate records.** Call `listEmployeeTrainings` (`GET /employee_trainings`) to retrieve every employee-training object in the company. Each record links an `employee_id` to a `training_id` and carries `completion_status` and `completion_rate`.
2. **Inspect one record (optional).** Call `getEmployeeTraining` (`GET /employee_trainings/{employee_training_id}`) for a single record's current state.
3. **Update completion.** Call `updateEmployeeTraining` (`PATCH /employee_trainings/{employee_training_id}`) with:
   - `completion_status` (boolean) — when `true`, Haekka overrides `completion_rate` to `100`.
   - `completion_rate` (integer, 0–100) — partial progress; ignored if `completion_status` is `true`.

## Conventions and errors
- Because `completion_status: true` forces `completion_rate` to 100, send `completion_rate` alone for partial progress and only set `completion_status` when the training is truly finished.
- No idempotency key — treat updates as last-write-wins and re-read with `getEmployeeTraining` to confirm.
- `404` means the `employee_training_id` is unknown; `400` means the body is malformed (see `errors/haekka-problem-types.yml`).
