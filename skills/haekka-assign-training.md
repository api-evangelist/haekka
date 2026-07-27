---
name: Assign a training to employees
description: Look up trainings and active employees, then assign a training to a set of employees via the Haekka Public API.
api: openapi/haekka-public-api-openapi.yml
operations: [listTrainings, listEmployees, assignTraining]
---

# Assign a training to employees

Use the Haekka Public API to assign a training course to one or more active employees.

## Authentication
All requests use a bearer token. Generate an API key in Haekka account settings and send it as:

```
Authorization: Bearer {key}
```

Base URL: `https://app.haekka.com/api/public`

## Steps

1. **Find the training.** Call `listTrainings` (`GET /trainings`) and select the `training_id` of the course to assign. To confirm details, call `getTraining` (`GET /trainings/{training_id}`).
2. **Find the employees.** Call `listEmployees` (`GET /employees`) to get the `id` of each active employee who should receive the training. Only active employees are returned.
3. **Assign.** Call `assignTraining` (`POST /trainings/assign`) with a JSON body:
   - `training_id` (required) — the training to assign.
   - `employee_ids` (required) — the list of employee ids.
   - `alert_employees` (optional, boolean, defaults `true`) — set `false` to assign silently without notifying employees.

## Conventions and errors
- Media type is `application/json` (see `conventions/haekka-conventions.yml`).
- No idempotency key is supported — do not blindly retry `assignTraining` on an ambiguous response; re-check state first.
- A `400` means required fields are missing; `401` means the API key is missing or invalid (see `errors/haekka-problem-types.yml`).
