# api-pagination

## Purpose

Standard request/response shape for paginated list endpoints across microservices.

## Requirements

### Requirement: Pagination request parameters
List endpoints that support pagination SHALL accept `page` (1-indexed integer, default 1) and `pageSize` (integer, service-defined default and maximum) query parameters.

#### Scenario: Default page returned when parameters omitted
- **WHEN** a client requests a paginated list endpoint without `page` or `pageSize` query parameters
- **THEN** the service SHALL respond as if `page=1` and its default `pageSize` were supplied

### Requirement: Pagination response envelope
Paginated list responses SHALL return a JSON body with a top-level `data` array and a `pagination` object containing at minimum `page`, `pageSize`, `hasNextPage`, and `hasPreviousPage`.

#### Scenario: List response includes required pagination fields
- **WHEN** a service returns a paginated list response
- **THEN** the response body SHALL contain `data` as an array and `pagination` as an object with `page`, `pageSize`, `hasNextPage`, and `hasPreviousPage`

### Requirement: Optional total counts
The `pagination` object MAY include `totalItems` and `totalPages`; when both are included, `totalPages` SHALL equal the ceiling of `totalItems` divided by `pageSize`.

#### Scenario: Total pages is consistent with total items when present
- **WHEN** a paginated response includes both `totalItems` and `totalPages`
- **THEN** `totalPages` SHALL equal the ceiling of `totalItems / pageSize`

### Requirement: Out-of-range page handling
Requesting a `page` beyond the last available page SHALL return a successful response with an empty `data` array and `hasNextPage` set to `false`, not an error.

#### Scenario: Page past the last page returns empty data
- **WHEN** a client requests a `page` number greater than the total number of available pages
- **THEN** the service SHALL return a 2xx response with `data` as an empty array and `pagination.hasNextPage` set to `false`
