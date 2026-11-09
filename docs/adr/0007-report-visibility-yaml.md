# ADR 007: YAML-Driven Report Visibility

## Status

Proposed

## Context

`reports.html` is a static dashboard that links from the repository to the per-module
analysis and test reports (Checkstyle, PMD, SpotBugs, JUnit, coverage, mutation, and
documentation artifacts under each module's `build/reports/`). Whether a dashboard
section or card is visible is currently controlled by a single hand-maintained rule in
`reports.css`: a block headed `/* === Control visibility of major report sections === */`
(lines 4–482) holds a comma-separated selector list terminated by one
`display: none;` declaration (line 482). Selectors are toggled by wrapping individual
lines in `/* ... */`; the block also embeds an ASCII wireframe of the Application card
layout and per-section banner comments.

This hand-toggled "visibility catalog" (408 selector lines, 42 currently active) has
several failure modes, one of which has already broken the dashboard:

1. **Silent whole-section hides.** The active selectors hide the entire
   `#lib-results`, `#api-results`, and `#template-results` sections, plus 32
   card-level selectors inside `#app-results`. The dashboard renders only part of the
   Application section; the other three sections are invisible. The mechanism cannot
   distinguish "hide a card type that has no report" (the intent) from "hide a whole
   section" (the accident).
2. **No recorded intent.** Nothing records *why* a selector is hidden. The commented-out
   lines are a graveyard of past experiments: duplicated selectors, inconsistent
   comment styles, and a dangling junk selector (`#template-resultsX`) that matches no
   element.
3. **No verification.** Nothing checks that the CSS agrees with the policy or with the
   dashboard HTML, and the current CI workflow (`pr-validation.yml`) only labels PRs —
   it runs no Gradle task — so drift is invisible until someone opens the page.
4. **Structural mismatch.** The four dashboard sections (`app`, `lib`, `api`,
   `template`) do not map to the three Gradle modules (`app`, `list`, `utilities`):
   `lib` and `template` are not modules, and `list` and `utilities` have no sections.
   Visibility therefore cannot be derived from the module layout and must be declared
   explicitly per section and per card.

## Decision

Report visibility becomes declarative: a YAML policy file is the single source of
truth, the visibility block of `reports.css` is generated from it, and a verification
task keeps the policy, the CSS, and the dashboard HTML in agreement.

### Policy File: `reports/visibility.yaml`

A new file at the repository root, next to `reports.html` / `reports.css`:

```yaml
version: 1

sections:                      # keyed by dashboard section, in dashboard order
  app:                         # <section id="app-results">
    label: Application
    hidden: false              # whole-section hide (must carry a `reason` if true)
    cards:                     # selectors relative to #app-results
      - selector: ".test-functional"
        hidden: true
        reason: "No functional-test report is wired into the template yet."
      - selector: ".main .dynamic-analysis .db"
        hidden: true
        reason: "No database report is generated for app."
  lib:
    label: Library
    hidden: false
    cards: []
  api:
    label: API
    hidden: false
    cards: []
  template:
    label: Template
    hidden: false
    cards: []
```

Schema rules:

- **Keys are section ids, not module names.** Sections are the unit the dashboard
  renders and the scope every selector is expressed in; the deliberate non-correspondence
  with Gradle modules (Context 4) means module names would mislead.
- `cards[].selector` is a CSS selector *relative to the section*; the generator prefixes
  it with `#<key>-results` (e.g. `#app-results .test-bdd`).
- Every `hidden: true` entry (section or card) requires a non-empty `reason` — the
  intent the current catalog lacks.
- `hidden: false` and empty `cards` are valid: they record "intentionally visible."
- Unknown keys or malformed entries fail `verifyReportsVisibility` (schema validation).

### Generator: `generateReports`

A Gradle task that rewrites the visibility block of `reports.css` from the policy:

- The block is delimited by marker comments on their own lines,
  `/* ===== visibility:generated start ===== */` … `/* ===== visibility:generated end ===== */`;
  the task replaces only the content between the markers. Everything outside the markers
  (the wireframe diagram, the rest of the stylesheet) is untouched byte-for-byte,
  keeping diffs reviewable.
- Rendering is deterministic and idempotent: for each section, in file order, it emits a
  `/* #<id>-results (label) */` banner; a whole-section hide renders as
  `#<id>-results { display: none; /* reason */ }`; hidden cards are grouped into one
  `display: none;` rule per section, with a `/* reason: ... */` comment above each
  selector.
- Inputs: `reports/visibility.yaml` and `reports.css`; output: `reports.css`. The task
  is up-to-date when the policy is unchanged, so it is cheap to run.

The generated block replaces the entire hand-maintained catalog: duplicate lines, the
junk `#template-resultsX` selector, and the inconsistent per-line commenting disappear.
The ASCII wireframe is preserved in the file just above the generated block as static
documentation.

### Verification: `verifyReportsVisibility`

A Gradle task that fails on any disagreement among the three artifacts. Checks:

1. **Completeness** — every active `<section id="…-results">` in `reports.html` has a
   `sections` key in the policy, and vice versa.
2. **Resolution** — every policy selector (section-level and card-level) matches at
   least one element of `reports.html`, evaluated with a real CSS selector engine
   (Jsoup, a build-logic-only dependency). This is the check that would have caught
   `#template-resultsX`.
3. **Intent** — every `hidden: true` entry carries a `reason`.
4. **Section-hide audit** — a whole-section `hidden: true` is *allowed* (a section may
   legitimately have no content) but is always reported as a warning, so the category
   of mistake that produced the current bug stays visible to reviewers.
5. **Drift** — the task re-renders the generated block from the policy and diffs it
   against the committed content of `reports.css`; a mismatch (policy edited but CSS not
   regenerated, or hand-edited CSS) fails the build. The policy and the generated block
   must be committed together.

### Enforcement: `checkReports` and CI

- `checkReports` aggregates `generateReports` and `verifyReportsVisibility` and is
  wired into the root project's `check` task, so `./gradlew build` runs it locally.
- A job is added to `.github/workflows/pr-validation.yml` (which today only labels PRs
  and runs no Gradle task) that runs `./gradlew checkReports`, so drift and junk
  selectors are caught on every pull request.

### Build Integration

The tasks are registered by a new precompiled convention plugin
`buildlogic.reports-conventions` in `build-logic` (matching the existing
`buildlogic.java-*-conventions` plugins, per ADR 001) and applied at the **root**
project. The root is used deliberately instead of a `subprojects { }` hook:
`reports.html` / `reports.css` are root artifacts, so the tasks have a single, correct
location rather than one redundant copy per module. The convention adds `snakeyaml`
(policy parsing) and `jsoup` (HTML verification) to `build-logic`'s dependencies;
neither reaches project or application code.

### Migration (fixes the current bug)

1. Author `reports/visibility.yaml` with all four sections `hidden: false` — this
   restores the currently invisible `lib`, `api`, and `template` sections.
2. Seed the `cards` lists with the 38 card-level selectors currently active in the
   catalog (32 in `app`, 6 in `template`; no card-level hides exist in `lib` / `api`),
   each with a `reason` stating which report it awaits.
3. Insert the markers around the existing block, run `generateReports`, and commit the
   result — the monolithic ~480-line catalog, its duplicates, and the junk selector are
   replaced by generated, documented rules.
4. Wire `check` and the CI step; from then on the CSS block is never hand-edited.

## Consequences

- **Positive:**
  - The dashboard bug is fixed: all four sections render again.
  - One source of truth with recorded intent; re-exposing or hiding a report is a
    one-line policy change, not a hunt through a 400-line comment graveyard.
  - The whole class of silent drift (dead selectors, policy/CSS divergence, hidden
    sections) becomes a build failure, locally and in CI.
  - The convention plugin keeps the root `build.gradle` declarative and the tasks
    reusable if a second dashboard or report type appears (ADR 001 principle).
- **Negative:**
  - New build-logic dependencies (`snakeyaml`, `jsoup`) and one more plugin to maintain.
  - In-place regeneration of a checked-in stylesheet is an unusual pattern; it is
    contained by the marker delimiters and the drift check, but contributors must
    understand that the generated region is not hand-editable.
  - The dashboard HTML becomes a contract: renaming a section id or card class without
    updating the policy fails the build. This is deliberate — the current system fails
    silently instead.
- **Out of scope:** generating the report *content* (the links and data inside the
  dashboard, e.g. emitting a card for a newly wired report) is a separate concern and
  is not addressed here; this ADR governs visibility only.


