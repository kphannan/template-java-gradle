# ADR 002: REST Service Baseline Standards

## Status

Proposed

## Context

To ensure consistency, interoperability, and ease of consumption across different services in the `rail-base` ecosystem, a set of baseline standards for RESTful APIs is required. Services may be implemented in any supported implementation language (ADR 0001); the standards below therefore separate a language-neutral core from per-language application.

## Decision

All REST services will adhere to the following language-neutral core standards:

- **API Style:** REST (Representational State Transfer).
- **Data Format:** JSON for all requests and responses.
- **Naming Convention:** `kebab-case` for URLs, `camelCase` for JSON properties.
- **HTTP Methods:**
  - `GET`: Retrieve resources.
  - `POST`: Create resources.
  - `PUT`: Replace resources.
  - `PATCH`: Partially update resources.
  - `DELETE`: Remove resources.
- **Error Handling:** Errors MUST be returned as RFC 9457 (Problem Details for HTTP APIs) documents: content type `application/problem+json`, with `type` (URI reference to a stable error code), `title` (short human-readable summary), `status` (HTTP status code), `detail` (human-readable explanation of this specific occurrence), and `instance` (URI identifying this specific occurrence). Each service MUST define a stable, documented set of error codes mapped to HTTP status codes.
- **Versioning:** URL-based versioning (e.g., `/api/v1/...`).
- **Health:** Every service MUST expose liveness and readiness endpoints.
- **Documentation:** Every endpoint MUST be documented in an OpenAPI document published with the service.
- **Security:** OAuth2 as the security standard.

### Java

Services implemented in Java satisfy the core standards using the Spring Boot stack:

- **Framework:** Spring Boot.
- **Health:** Spring Boot Actuator liveness/readiness probes.
- **Error Handling:** RFC 9457 responses produced by a central exception handler that maps domain exceptions to the service's error codes.
- **Documentation:** OpenAPI document generated from endpoint and DTO annotations.
- **Nullability:** JSpecify annotations, enforced at compile time.

### JavaScript/TypeScript

Services implemented in JavaScript/TypeScript satisfy the same core standards using a Node.js stack:

- **Framework:** Express or Fastify.
- **Health:** Liveness and readiness endpoints implemented in the framework.
- **Error Handling:** RFC 9457 responses produced by a central error middleware that maps domain errors to the service's error codes.
- **Documentation:** OpenAPI document derived from TypeScript types / JSDoc annotations, or maintained alongside the code.
- **Nullability:** TypeScript strict mode.

## Consequences

- **Positive:** Predictable API behavior, simplified client implementation, and better documentation.
- **Negative:** Strict adherence may require more effort during the initial development of non-standard endpoints.
- The named frameworks (Spring Boot; Express/Fastify) are the means of satisfying the core standards, not the standard itself: clients and other ADRs depend on the neutral core, so a per-language framework swap does not change the API contract.
