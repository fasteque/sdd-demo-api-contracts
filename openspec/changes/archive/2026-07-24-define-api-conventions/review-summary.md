# Review Summary — Platform API Conventions

Use this as the discussion doc when reviewing `define-api-conventions` with a
representative from each service team. Full detail is in `proposal.md`,
`design.md`, and `specs/`.

## What's being proposed

1. **Error responses** — every 4xx/5xx returns `{ "error": { "code", "message", "traceId", "details?" } }`.
2. **Pagination** — list endpoints take `page`/`pageSize` query params and return `{ "data": [...], "pagination": { "page", "pageSize", "hasNextPage", "hasPreviousPage", "totalItems?", "totalPages?" } }`.
3. **Naming/casing** — camelCase JSON fields and query params, kebab-case URL paths, Train-Case cross-cutting headers, SCREAMING_SNAKE_CASE enums/error codes.

## Questions for reviewers

- Does your service have an existing error/pagination shape that would be costly to migrate? What's the realistic timeline given "migrate at next breaking-change release"?
- Any concerns with camelCase query parameters given your framework's defaults?
- Any list endpoints where counting `totalItems`/`totalPages` is prohibitively expensive (the spec allows omitting them, but want to confirm this covers real cases)?
- Any objection to the per-service freedom on `error.details[].code` values (see `design.md` Open Questions)?

## How to record feedback

Capture feedback inline in this file (or link to meeting notes), then use
`/opsx:update define-api-conventions` to fold agreed changes back into the
spec files before checking off tasks 1.1/1.3 in `tasks.md`.

**Status:** Not yet reviewed with service teams.
