---
name: Post a job and review candidate matches (recruiter)
description: Authenticate as a recruiter, create a job, review its candidate matches, progress a candidate, and share the job using the NoBueno Jungle API.
api: openapi/nobueno-openapi-original.json
operations:
  - login_create
  - refresh_create
  - jobs_create
  - jobs_read
  - jobs_candidates
  - jobs_matches
  - jobs_share_job
---

# Post a job and review candidate matches (recruiter)

Operating instructions for an agent acting on behalf of a recruiter/company on the NoBueno "Jungle API" (`https://api.nobueno.com/api`).

## Auth
1. Obtain a token with `login_create` (`POST /api/login`); send it as `Authorization: Bearer <token>` on every call, with the `Device-Type` header. Renew with `refresh_create` (`POST /api/refresh`).

## Steps
1. Create the posting with `jobs_create` (`POST /api/jobs`). Capture the returned job `id`.
2. Read it back with `jobs_read` (`GET /api/jobs/{id}`) to confirm it persisted.
3. Review matched candidates for the job with `jobs_candidates` (`GET /api/jobs/{id}/candidates`), and the platform-computed matches with `jobs_matches` (`GET /api/jobs/matches`). Both are paginated (`page`/`perPage`, envelope `{count,next,previous,results}`); use `search`/`ordering` to focus.
4. To distribute the posting, call `jobs_share_job` (`POST /api/jobs/{id}/share`).

## Rules
- Mutating calls are not idempotent; on a failed `jobs_create` re-list (`GET /api/jobs`) before recreating to avoid duplicate postings.
- Page through candidate lists — the strongest matches are not guaranteed to be first unless you set `ordering`.
- Non-2xx responses follow the DRF default error envelope (no published problem+json schema).
