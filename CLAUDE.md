# CLAUDE.md - rail-base

## Build and Test Commands

- **Build project**: `./gradlew build`
- **Run application**: `./gradlew run` (assuming `app` project is configured for execution)
- **Run all tests**: `./gradlew test`
- **Clean build**: `./gradlew clean`

## Environment Requirements

- **`GIT_CONFIG_NOSYSTEM`**: The `net.nemerosa.versioning` plugin resolves the project version during configuration, and the JGit it bundles forks `git` twice on the first repository open — a Configuration Cache problem that fails builds on Gradle ≥ 8.7 (upstream: [nemerosa/versioning#97](https://github.com/nemerosa/versioning/issues/97), open). Export `GIT_CONFIG_NOSYSTEM=true` in the Gradle daemon's environment and restart the daemon (`./gradlew --stop`). JGit then skips system gitconfig discovery (no forks); `~/.gitconfig` and the repository config are still read from disk, and versioning only reads local repository state, so behavior is unchanged.
- **`org.gradle.configuration-cache-problems=warn`** in `gradle.properties` is a temporary safety net for environments without that variable. It masks real configuration-cache regressions, so remove it once the environment variable is standard.

## Code Style Guidelines

### Java

- **Naming Conventions**:
  - Classes: `PascalCase` (e.g., `MessageUtils`)
  - Methods/Variables: `camelCase` (e.g., `getMessage()`, `tokens`)
  - Packages: Lowercase, reverse domain name (e.g., `org.example.app`)
- **Formatting**:
  - Follow standard Java conventions.
  - Use `lombok` for boilerplate reduction where applicable (as seen in build configuration).
- **Structure**:
  - Multi-module Gradle project: `app` (application), `utilities` (shared logic), `list` (data structures).
  - Source code located in `src/main/java` and tests in `src/test/java`.

### Gradle

- **DSL**: Use the Groovy DSL (`.gradle` files) for all build scripts.

### Git Commits

- **Standard**: All commit messages must follow the [Conventional Commits](https://www.conventionalcommits.org/) specification.
- **Format**: `<type>[optional scope]: <description>` (e.g., `feat(app): add new message utility`).

## Project Structure

- `/app`: Main application logic.
- `/utilities`: Utility classes used across the project.
- `/list`: Core data structure implementations (e.g., `LinkedList`).
- `/build-logic`: Gradle convention plugins for build consistency.

## Agent skills

### Issue tracker

Issues are tracked via GitHub Issues. See `docs/agents/issue-tracker.md`.

### Triage labels

Five canonical triage roles, using the default label strings ( `needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix` ).  See `docs/agents/triage-labels.md`.

### Domain docs

Single-context layout (root `GLOSSARY.md` and `docs/adr/`). See `docs/agents/domain.md`.

