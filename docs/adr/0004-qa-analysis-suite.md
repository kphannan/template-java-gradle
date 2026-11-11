# ADR 0004: Quality Assurance and Analysis Suite

## Status

Proposed

## Context

To maintain high software quality and prevent regressions, a comprehensive QA and analysis suite is necessary. Because `rail-base` supports multiple implementation languages (ADR 0001), the suite is defined as a set of language-neutral layers, each applied with per-language tooling.

## Decision

The QA suite consists of the following layers, applied to every module regardless of implementation language:

1. **Static Analysis:** Style, formatting, correctness, and potential-bug detection on all source.
2. **Automated Testing**
   - **Unit Tests:** High coverage of business logic.
   - **Integration Tests:** Testing interactions between modules and external dependencies.
   - **End-to-End (E2E) Tests:** Critical-path testing where the module exposes a browser UI (JavaScript/TypeScript modules); Java services are covered at the API level by integration tests.
3. **Mutation Testing:** Production code is mutated to verify that the unit tests actually detect defects; applied where a mature mutation-testing tool exists for the language.
4. **Performance Testing:** Load and performance testing with **Gatling** — the single performance-testing tool for all supported languages (official Java and JavaScript SDKs), run in CI and reported alongside the other layers.
5. **Code Analysis:** Integration of SonarQube or a similar platform for tracking technical debt and code coverage, across languages.

### Java

- **Static Analysis:** Checkstyle (style), PMD (common coding flaws), SpotBugs (potential runtime defects, including the FindSecBugs security ruleset); Spotless for formatting and OpenRewrite for safe refactoring.
- **Unit Tests:** JUnit (Jupiter).
- **Coverage:** JaCoCo.
- **Mutation Testing:** Pitest, wired into the check lifecycle.
- **Performance Testing:** Gatling with the Java SDK.

### JavaScript/TypeScript

- **Static Analysis:** TypeScript compiler (strict mode), ESLint (correctness and potential bugs), Biome (formatting).
- **Unit Tests:** Vitest, with coverage.
- **E2E Tests:** Playwright or Cypress.
- **Performance Testing:** Gatling with the JavaScript SDK.

## Consequences

- **Positive:** Higher confidence in releases, fewer production bugs, and improved code maintainability.
- **Negative:** Increased development time per feature due to test writing requirements.
- One performance-testing tool (Gatling) serves all languages, so results are comparable across the ecosystem.
- Secure coding standards (candidate frameworks: ISO/IEC 27034, OWASP ASVS, SEI CERT) are under separate research tracked in ADR 0006; once adopted, they extend the static analysis layer.
