# ADR 001: Build Logic Architecture

## Status

Proposed

## Context

The project requires a robust and scalable build system that can handle multiple environments, target platforms, and dependency management without becoming a bottleneck.

The `rail-base` ecosystem is not single-language: applications and modules may be implemented in Java or in JavaScript/TypeScript (see GLOSSARY.md, "Implementation Language"). The build logic must serve every supported implementation language with one consistent architecture rather than per-language ad-hoc builds.

## Decision

We will implement a build logic architecture based on the following principles:

- **Declarative Configuration:** Use configuration files (e.g., JSON, YAML) to define build targets and environment variables.
- **Modular Build Scripts:** Break down the build process into small, reusable scripts (e.g., `compile`, `test`, `package`, `deploy`).
- **CI/CD Integration:** The build logic will be designed to run identically in local development and CI/CD pipelines (e.g., GitHub Actions, GitLab CI).
- **Artifact Versioning:** Implement semantic versioning for all build artifacts to ensure traceability.

These principles apply to every implementation language, realized per language as follows:

### Java

- **Build Tool:** Gradle (Groovy DSL).
- **Convention Plugins:** Reusable build behavior lives in convention plugins under `build-logic/` (`buildlogic.java-common-conventions`, `buildlogic.java-library-conventions`, `buildlogic.java-application-conventions`); individual module builds stay declarative.
- **Dependency Management:** The Gradle version catalog (`gradle/libs.versions.toml`) is the single source of truth for dependency versions.

### JavaScript/TypeScript

- **Package Manager:** `pnpm` is the sole package manager.
- **Build Tool:** Vite (esbuild).
- **Language:** TypeScript in strict mode.

Adding a new implementation language requires a new (or superseding) ADR.

## Consequences

- **Positive:** Increased reliability of builds, easier onboarding for new developers, and reduced "it works on my machine" issues.
- **Negative:** Initial complexity in setting up the build orchestration layer.
- Each implementation language carries its own convention/tooling layer, so per-language specifics stay out of the neutral core and remain portable.
