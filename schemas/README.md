# Platform API Contract Schemas

Shared JSON Schema definitions for the platform-wide API conventions defined in
`openspec/specs/api-error-responses/spec.md` and `openspec/specs/api-pagination/spec.md`.

Service teams should reference these schemas from their own OpenAPI/JSON Schema
definitions rather than redefining the shapes locally:

- [`error-response.schema.json`](./error-response.schema.json) — the `error` envelope returned on every 4xx/5xx response.
- [`pagination-response.schema.json`](./pagination-response.schema.json) — the `data` + `pagination` envelope returned by paginated list endpoints. Compose your endpoint's item schema into `data` via `$ref`/`allOf`.

Naming and casing rules (JSON fields, URL paths, query params, headers, enums)
are documented in `openspec/specs/api-naming-conventions/spec.md` and summarized
in `docs/api-guidelines.md`.
