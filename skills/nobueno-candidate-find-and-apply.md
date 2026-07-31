---
name: Find and apply to matched jobs (candidate)
description: Authenticate as a job-seeker, review recommended and matched jobs, act on a match, and message a recruiter using the NoBueno Jungle API.
api: openapi/nobueno-openapi-original.json
operations:
  - register_create
  - login_create
  - refresh_create
  - me_list
  - users_recommended_jobs
  - users_matches
  - users_recommended-jobs_job_change_status
  - messages_send_message
---

# Find and apply to matched jobs (candidate)

Operating instructions for an agent acting on behalf of a job-seeker on the NoBueno "Jungle API" (`https://api.nobueno.com/api`).

## Auth
1. If the user has no account, create one with `register_create` (`POST /api/register`). Otherwise obtain a token with `login_create` (`POST /api/login`).
2. Send the token on every subsequent call as `Authorization: Bearer <token>`. Also set the `Device-Type` header. Renew an expired token with `refresh_create` (`POST /api/refresh`) rather than re-logging-in.

## Steps
1. Confirm the signed-in profile with `me_list` (`GET /api/me`).
2. Pull the personalized job feed with `users_recommended_jobs` (`GET /api/users/recommended-jobs`) and the candidate's active matches with `users_matches` (`GET /api/users/matches`).
3. These are list endpoints — page through with `page` and `perPage`; the response envelope is `{count, next, previous, results}`. Narrow with `search` and sort with `ordering`.
4. To act on a recommended job (e.g. accept / decline / progress), call `users_recommended-jobs_job_change_status` (`PATCH /api/users/recommended-jobs/{job_id}/change_status`) with the target status.
5. To reach the recruiter on a match, use `messages_send_message` (`POST /api/messages/{id}/send-message`).

## Rules
- No idempotency key is supported; do not blindly retry a `PATCH`/`POST` on a network error — re-read state first (`users_matches`) before retrying.
- Respect pagination; never assume all results are on page 1.
- The spec does not publish an error schema; treat non-2xx as a DRF error envelope (`{"detail": ...}` or field-keyed validation arrays).
