# GLOSSARY.md - rail-base

This document provides definitions for key terms, classes, and concepts used within the `rail-base` codebase.

## Core Data Structures

- **LinkedList**: A custom implementation of a singly linked list that stores `String` elements. It provides basic operations such as adding, removing, and retrieving elements by index.
- **Node**: A private static inner class of `LinkedList` representing a single element in the list. It contains the `data` (String) and a reference to the `next` node in the sequence.

## Utility Concepts

- **Split**: The process of breaking a single `String` into multiple tokens based on whitespace, which are then stored in a `LinkedList`.
- **Join**: The process of concatenating all elements of a `LinkedList` back into a single `String`, separated by spaces.
- **Token**: A single unit of text resulting from a `split` operation.

## Project Modules

- **app**: The entry point of the project, coordinating the flow between utilities and data structures to perform a simple "split-then-join" operation.
- **utilities**: A collection of helper classes (`StringUtils`, `SplitUtils`, `JoinUtils`) that provide high-level string manipulation logic.
- **list**: The foundation module containing the custom `LinkedList` implementation used by other modules.

## Key Classes

- **App**: The main class containing the `main` method; it demonstrates the project's functionality.
- **MessageUtils**: A helper class in the `app` module used to provide sample input strings.
- **StringUtils**: A facade class in the `utilities` module that simplifies access to `SplitUtils` and `JoinUtils`.
- **SplitUtils**: Contains the internal logic for splitting strings into a `LinkedList`.
- **JoinUtils**: Contains the internal logic for joining a `LinkedList` into a string.

## Reporting & Visibility

- **Report Dashboard**: The static `reports.html` page (styled by `reports.css`) that links from the repository to the per-module analysis and test reports generated under each module's `build/reports/` directory.
- **Section**: A top-level dashboard panel for one codebase area (`app`, `lib`, `api`, `template`), rendered as `<section id="<name>-results">`. Sections do not map one-to-one to Gradle modules.
- **Card**: A dashboard element (an `<li>` or panel such as `.static-analysis` or `.test-bdd`) representing one report type, nested inside a section.
- **Visibility Policy**: `reports/visibility.yaml` — the single source of truth for which sections and cards are hidden, per ADR 0007. Every hidden entry must record a `reason`.
- **Generated Block**: The marker-delimited region of `reports.css` (between the `visibility:generated` start/end comments) that the `generateReports` task rewrites from the Visibility Policy. It must not be hand-edited.
