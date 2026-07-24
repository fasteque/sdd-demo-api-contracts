# api-naming-conventions

## Purpose

Platform-wide naming and casing rules for JSON fields, URL paths, query parameters, headers, and enum values.

## Requirements

### Requirement: JSON field casing
All JSON field names in request and response bodies SHALL use camelCase.

#### Scenario: Response body fields use camelCase
- **WHEN** a service returns a JSON response body
- **THEN** every field name in that body SHALL be camelCase, not snake_case or kebab-case

### Requirement: URL path casing and structure
URL path segments SHALL use lowercase kebab-case, and collection resource names SHALL be pluralized nouns.

#### Scenario: Collection endpoint path is plural kebab-case
- **WHEN** a new collection endpoint is defined
- **THEN** its path segment SHALL be a lowercase, pluralized, kebab-case noun (e.g., `/order-items`)

### Requirement: Query parameter casing
Query parameter names SHALL use camelCase, matching the casing used for the same field when it appears in a JSON body.

#### Scenario: Query parameter matches body field casing
- **WHEN** a query parameter corresponds to a field also present in a JSON body, such as a sort or filter field
- **THEN** the query parameter name SHALL use the identical camelCase spelling as the JSON field

### Requirement: HTTP header naming
Platform-wide cross-cutting HTTP headers SHALL use standard Train-Case naming without service-specific `X-` prefixes.

#### Scenario: Trace/idempotency headers use unprefixed Train-Case names
- **WHEN** a service emits or accepts a platform-wide header for tracing or idempotency
- **THEN** the header name SHALL be Train-Case and SHALL NOT use a service-specific `X-` prefix (e.g., `Request-Id`, `Idempotency-Key`)

### Requirement: Enum and error code casing
Enum values and error codes exposed in APIs SHALL use SCREAMING_SNAKE_CASE.

#### Scenario: Enum value uses SCREAMING_SNAKE_CASE
- **WHEN** a service defines an enumerated value exposed via the API, including error codes
- **THEN** the value SHALL be formatted as SCREAMING_SNAKE_CASE
