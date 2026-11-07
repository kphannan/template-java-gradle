# ADR 002: REST Service Baseline Standards

## Status

Proposed

## Context

To ensure consistency, interoperability, and ease of consumption across different services in the `rail-base` ecosystem, a set of baseline standards for RESTful APIs is required.

## Decision

All REST services will adhere to the following standards:

- **API Style:** REST (Representational State Transfer).
- **Data Format:** JSON for all requests and responses.
- **Naming Convention:** `kebab-case` for URLs, `camelCase` for JSON properties.
- **HTTP Methods:**
  - `GET`: Retrieve resources.
  - `POST`: Create resources.
  - `PUT`: Replace resources.
  - `PATCH`: Partially update resources.
  - `DELETE`: Remove resources.
- **Error Handling:** Standardized error response body containing an error code and a human-readable message.
- **Versioning:** URL-based versioning (e.g., `/api/v1/...`).
- Actuator Liveness/Readiness probes.
- Mandatory OpenAPI annotations for endpoints and DTOs.
- JSpecify for nullability.
- OAuth2 as the security standard.


## Consequences

- **Positive:** Predictable API behavior, simplified client implementation, and better documentation.
- **Negative:** Strict adherence may require more effort during the initial development of non-standard endpoints.
