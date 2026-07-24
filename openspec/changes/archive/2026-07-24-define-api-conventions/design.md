## Context

The platform has multiple independently-deployed microservices, each exposing HTTP/JSON APIs. There is currently no shared contract for how errors are reported, how paginated lists are shaped, or how names/casing are chosen across bodies, paths, params, and headers. Each service has grown its own conventions organically, so shared client code (SDKs, error banners, list/table components) cannot rely on a single parsing path. This change defines the platform-wide contract; it does not implement it in any service.

Stakeholders: all service-owning teams (must conform going forward), API gateway/platform team (may enforce or validate), and API consumers (internal services, frontend, third parties) who benefit from predictable shapes.

## Goals / Non-Goals

**Goals:**
- Define one canonical JSON shape for 4xx/5xx error responses, usable by any client without per-service branching.
- Define one canonical JSON shape for paginated list responses, including the query parameters used to request a page.
- Define naming/casing rules for JSON fields, URL paths, query parameters, HTTP headers, and enum/error-code values.
- Make the conventions specific and testable enough that a future linter/contract-test suite can enforce them mechanically.

**Non-Goals:**
- Implementing the shapes in any specific service or shared library (follow-up work per service).
- Building enforcement tooling (CI linting, OpenAPI schema validation) — noted as an open question / future change.
- Defining cursor-based or streaming pagination (only page-based pagination is in scope; cursor-based is an open question).
- Defining authentication, rate-limiting, or versioning schemes.

## Decisions

### 1. Error responses use a nested `error` envelope, not flat top-level fields
```json
{
  "error": {
    "code": "VALIDATION_FAILED",
    "message": "The request could not be processed.",
    "traceId": "3f7a1c9e-2b7a-4e9d-9c2f-1a2b3c4d5e6f",
    "details": [
      { "field": "email", "code": "INVALID_FORMAT", "message": "must be a valid email address" }
    ]
  }
}
```
- HTTP status code itself remains the source of truth for the response's status class; it is not duplicated inside the body, avoiding drift between the two.
- `code` is a stable, machine-readable SCREAMING_SNAKE_CASE identifier clients can switch on; `message` is human-readable and may change wording without being a breaking change.
- `details` is optional and only present for validation-style errors (typically 400/422) needing field-level granularity.
- `traceId` ties the response to server-side logs/tracing for support and debugging.
- Alternative considered: flat top-level fields (`code`, `message` at the root). Rejected because it collides with successful-response fields if a client inspects shape generically, and gives no room to add error-only metadata later without risking collisions.
- Alternative considered: full RFC 7807 Problem Details (`type`, `title`, `status`, `detail`, `instance`). Rejected as heavier than needed for an internal platform; the `error`-envelope shape keeps the same spirit (stable code + human message) with names consistent with the rest of the platform's camelCase convention.

### 2. Pagination is page-based, returned as `data` + `pagination` envelope
```json
{
  "data": [ { "...": "..." } ],
  "pagination": {
    "page": 1,
    "pageSize": 20,
    "totalItems": 134,
    "totalPages": 7,
    "hasNextPage": true,
    "hasPreviousPage": false
  }
}
```
- Request query params: `page` (1-indexed, default 1) and `pageSize` (default and max defined per-service, but the parameter names themselves are fixed).
- `totalItems`/`totalPages` are included so clients can render page controls without a second request; services for which counting is prohibitively expensive may omit them (documented as an allowed exception in the spec) but must always include `page`, `pageSize`, `hasNextPage`, `hasPreviousPage`.
- Alternative considered: cursor-based pagination (`nextCursor`/`prevCursor`). Rejected for this change because most current list endpoints are backed by paginatable stores where offset/page is sufficient; flagged as an open question for high-volume/streaming endpoints later.
- Alternative considered: returning the array directly at the top level with pagination in headers (e.g., `Link`, `X-Total-Count`). Rejected because it splits the contract across body and headers, which is harder for generic client code to consume and easier to drop when proxies rewrite headers.

### 3. Naming/casing rules by surface
- **JSON field names**: `camelCase` everywhere (request and response bodies), including inside `error` and `pagination` envelopes.
- **URL paths**: lowercase `kebab-case` path segments, plural nouns for collections (`/order-items`, not `/orderItem` or `/OrderItems`), resource IDs as path parameters (`/orders/{orderId}`).
- **Query parameters**: `camelCase`, matching JSON body casing so the same field name looks identical whether it's filtering, sorting, or appearing in a response body (e.g., `?pageSize=20&sortBy=createdAt`).
- **HTTP headers**: use standard `Train-Case` HTTP header conventions (e.g., `Idempotency-Key`, `Request-Id`); no service-specific `X-`-prefixed headers for concerns that are platform-wide (trace/request id, idempotency).
- **Enum and error-code values**: `SCREAMING_SNAKE_CASE` (e.g., `VALIDATION_FAILED`, `ORDER_STATUS_CANCELLED`), so they are visually distinct from field names and safe to use as code constants across languages.
- Alternative considered: `snake_case` for query params and JSON fields (common in some public APIs). Rejected to keep a single casing rule across body and query string, minimizing translation code in shared client libraries.

## Risks / Trade-offs

- **[Risk]** Existing services already ship divergent shapes and casing → clients depending on the old shapes could break if services migrate abruptly. **Mitigation**: conventions are normative for new services immediately; existing services migrate on their next breaking-change/major-version release, not retroactively.
- **[Risk]** camelCase query parameters are non-default in some web frameworks that favor snake_case query binding → risk of inconsistent adoption. **Mitigation**: call out explicitly in the naming spec and treat it as a per-service integration detail to solve with framework config or a thin adapter, not a reason to deviate from the convention.
- **[Risk]** Omitting `totalItems`/`totalPages` for expensive-to-count collections creates a documented inconsistency between endpoints. **Mitigation**: the pagination spec requires `page`, `pageSize`, `hasNextPage`, `hasPreviousPage` unconditionally, and treats total counts as optional-but-recommended, so client code can rely on a consistent minimum subset.
- **[Risk]** No enforcement mechanism exists yet, so drift can reappear even after adoption. **Mitigation**: out of scope here, but flagged as an open question / natural follow-up change (shared OpenAPI components, CI contract-test/lint).

## Migration Plan

1. Publish this spec as the platform's normative API convention (this change).
2. New services and new endpoints on existing services must conform starting immediately after this change is accepted.
3. Existing endpoints that don't conform are migrated opportunistically at each service's next breaking-change/major-version release; no forced retrofit is required by this change.
4. Follow-up (separate change, not in this scope): publish shared JSON Schema/OpenAPI components for the error and pagination shapes, and consider CI-level contract tests or linting to catch drift automatically.

Rollback: since this change only introduces a written specification (no running code), rollback is simply reverting/superseding the spec files; no deployed system is affected directly by this change alone.

## Open Questions

- **Cursor-based pagination**: Deferred. Page-based pagination (this change) covers current list endpoints; a cursor-based shape for high-volume/streaming endpoints is left for a future change if/when such an endpoint is needed.
- **`details[].code` enum ownership**: Resolved as per-service freedom. The spec only constrains the envelope shape and casing (`SCREAMING_SNAKE_CASE`); individual field-level codes are defined by each service, since centralizing them would create a cross-service coupling point with no clear owner today.
- **Enforcement ownership and mechanism**: Deferred to the follow-up change tracked in task 4.1 (`openspec/changes/define-api-contract-enforcement`), which will decide between schema validation, CI contract tests, or linting, and assign an owning team.
