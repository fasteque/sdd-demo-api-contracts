# api-error-responses

## Purpose

Standard error response body shape and rules for all 4xx/5xx HTTP responses across microservices.

## Requirements

### Requirement: Standard error envelope
Every HTTP response with a 4xx or 5xx status code SHALL return a JSON body with a top-level `error` object containing `code`, `message`, and `traceId` string fields.

#### Scenario: Any error response includes required error fields
- **WHEN** a service returns an HTTP response with a 4xx or 5xx status code
- **THEN** the response body SHALL be JSON containing a top-level `error` object with `code`, `message`, and `traceId` string fields

### Requirement: Stable, machine-readable error codes
The `error.code` field SHALL be a SCREAMING_SNAKE_CASE string that is stable across releases and does not change when the human-readable `error.message` text changes.

#### Scenario: Error code remains stable when message text changes
- **WHEN** the error message text for a given error condition is edited
- **THEN** the corresponding `error.code` value SHALL remain unchanged

### Requirement: Field-level validation details
When a 400 or 422 response results from request validation failures, the `error.details` field SHALL be present as an array of objects, each with `field`, `code`, and `message`.

#### Scenario: Validation failure includes per-field details
- **WHEN** a request fails validation on one or more fields
- **THEN** the response SHALL return `error.details` as an array where each entry includes `field`, `code`, and `message`

### Requirement: HTTP status code is the sole status representation
The HTTP status code SHALL be the only representation of the response's status class; the error body SHALL NOT duplicate the numeric status code in a separate body field.

#### Scenario: Error body omits duplicate status field
- **WHEN** a service constructs an error response body
- **THEN** the body SHALL NOT include a field that repeats the HTTP status code
