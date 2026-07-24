## Why

Microservices in this platform currently have no shared contract for error responses, pagination, or naming/casing. Each team invents its own shape, which forces every client (internal and external) to write per-service parsing logic, makes generic error handling and pagination helpers impossible to share, and slows down onboarding of new services. Defining these conventions now, before more services ship divergent APIs, lets us enforce consistency via shared schemas/linting instead of retrofitting every service later.

## What Changes

- Define a standard JSON error response shape for all 4xx/5xx responses (status code, machine-readable error code, human-readable message, optional field-level validation details, trace/request id).
- Define a standard JSON pagination response shape for list endpoints (page-based, covering item collection, page metadata, and navigation cues).
- Define naming and casing rules covering: JSON field casing, URL path casing and structure, query parameter naming, HTTP header naming, and enum/error-code value casing.
- These conventions are normative for all new microservices immediately and existing services must migrate on their next breaking-change release; no code is implemented in this change, only the contract.

## Capabilities

### New Capabilities
- `api-error-responses`: Standard error response body shape and rules for all 4xx/5xx HTTP responses across microservices.
- `api-pagination`: Standard request/response shape for paginated list endpoints across microservices.
- `api-naming-conventions`: Platform-wide naming and casing rules for JSON fields, URL paths, query parameters, headers, and enum values.

### Modified Capabilities
- None — this is a greenfield set of platform conventions; no existing specs are affected.

## Impact

- **Affected systems**: All current and future microservices exposing HTTP APIs on the platform.
- **Affected code**: None directly in this change; downstream, each service's API layer (error handlers, list endpoint serializers, DTOs/schemas) will need to conform.
- **Dependencies**: API gateway / shared client SDKs may need updates to validate or rely on the new shapes once adopted.
- **Consumers**: Frontend clients, internal service-to-service callers, and third-party API consumers all depend on these shapes being stable once published.
