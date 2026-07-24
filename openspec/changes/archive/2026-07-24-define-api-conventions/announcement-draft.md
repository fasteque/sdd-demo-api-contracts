# Announcement Draft — Platform API Conventions

**Status:** Draft, not yet sent. Send once `review-summary.md` feedback is incorporated.

---

**Subject: New platform-wide API conventions — error responses, pagination, naming**

Hi all,

We've published platform-wide conventions that all microservices with HTTP APIs
must follow going forward:

- **Standard error shape** for every 4xx/5xx response (`error.code`, `error.message`, `error.traceId`, optional `error.details`).
- **Standard pagination shape** for list endpoints (`page`/`pageSize` query params, `data` + `pagination` response envelope).
- **Naming/casing rules** for JSON fields, URL paths, query params, headers, and enum values.

**Full spec:** `openspec/specs/api-error-responses/`, `openspec/specs/api-pagination/`, `openspec/specs/api-naming-conventions/`
**Quick reference:** `docs/api-guidelines.md`
**Shared schemas:** `schemas/error-response.schema.json`, `schemas/pagination-response.schema.json`

**Migration policy:** New services and new endpoints must conform immediately.
Existing endpoints migrate at your service's next breaking-change/major-version
release — no forced retrofit required.

Questions or concerns? Reply here or reach out to [owning team — TBD, see
`ownership-candidates.md`].

Thanks,
[sender]
