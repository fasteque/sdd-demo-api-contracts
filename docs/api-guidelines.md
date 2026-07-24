# Platform API Guidelines

These are the platform-wide conventions all microservices exposing HTTP APIs must follow.
Full normative requirements live in `openspec/specs/`; shared JSON Schemas live in `schemas/`.

## Error responses (4xx/5xx)

Every error response body is a JSON object with a top-level `error` envelope:

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

- `code`: stable, `SCREAMING_SNAKE_CASE`, machine-readable. Never changes when `message` wording changes.
- `message`: human-readable, may change freely.
- `traceId`: correlates the response to server-side logs/tracing.
- `details`: optional, only for field-level validation errors (typically 400/422).
- The HTTP status code is the only representation of status class — never duplicated in the body.

See `openspec/specs/api-error-responses/spec.md` and `schemas/error-response.schema.json`.

## Pagination

Paginated list endpoints accept `page` (1-indexed, default 1) and `pageSize` query parameters, and respond with:

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

- `page`, `pageSize`, `hasNextPage`, `hasPreviousPage` are always required.
- `totalItems`/`totalPages` are recommended but may be omitted when counting is prohibitively expensive.
- Requesting a page past the last one returns a normal 2xx response with an empty `data` array, not an error.

See `openspec/specs/api-pagination/spec.md` and `schemas/pagination-response.schema.json`.

## Naming and casing

| Surface | Rule | Example |
|---|---|---|
| JSON fields (request & response bodies) | camelCase | `createdAt` |
| URL path segments | lowercase kebab-case, plural collection nouns | `/order-items/{orderItemId}` |
| Query parameters | camelCase, matching body field casing | `?pageSize=20&sortBy=createdAt` |
| HTTP headers (platform-wide, cross-cutting) | Train-Case, no service-specific `X-` prefix | `Request-Id`, `Idempotency-Key` |
| Enum / error code values | SCREAMING_SNAKE_CASE | `ORDER_STATUS_CANCELLED` |

See `openspec/specs/api-naming-conventions/spec.md` for full requirements.

## Adoption

New services and new endpoints must conform immediately. Existing endpoints migrate
at their service's next breaking-change/major-version release — no forced retrofit.
