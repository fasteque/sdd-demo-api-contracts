## Why

`define-api-conventions` established the platform's normative error response, pagination, and naming/casing rules, but conformance today relies entirely on manual review. Without a mechanical check, drift will reappear as new endpoints and services ship. This follow-up is filed to plan how conformance gets enforced automatically, rather than solving it as an afterthought.

## What Changes

- Decide the enforcement mechanism: shared OpenAPI/JSON Schema validation, CI-run contract tests, or static linting (or a combination).
- Decide which team owns building and maintaining the enforcement tooling (candidates: API platform/gateway team, or a rotating responsibility across service teams) — carried over as an open question from `define-api-conventions`.
- Wire the chosen mechanism into each service's CI pipeline (scope/rollout order to be defined in this change's design).

## Capabilities

### New Capabilities
- `api-contract-enforcement`: Automated checks (CI and/or gateway-level) that verify a service's error responses, pagination responses, and naming/casing conform to the conventions defined by `api-error-responses`, `api-pagination`, and `api-naming-conventions`.

### Modified Capabilities
- None yet identified.

## Impact

- **Affected systems**: CI pipelines for all microservices; potentially the API gateway if validation happens at the edge.
- **Affected code**: New tooling/CI jobs; no changes to the convention specs themselves.
- **Status**: This proposal is filed as a backlog placeholder. Design, specs, and tasks are not yet written — pick this change back up with `/opsx:propose define-api-contract-enforcement` (or `/opsx:update`) when ready to scope it.
