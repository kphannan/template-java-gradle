# Design Document - rail-base

## Overview

`rail-base` is a reference implementation of a multi-module Java project managed by Gradle. Its primary purpose is to serve as a template for build configurations, demonstrating a clean separation of concerns across different architectural layers.

## Build System Design

To ensure build consistency and reduce duplication, this project utilizes **Gradle Convention Plugins**.

- **Convention Location**: All shared build logic and convention plugins reside in the `/build-logic` directory.
- **Purpose**: This approach decouples the "how to build" (conventions) from the "what to build" (module declarations), allowing for centralized management of compiler flags, dependency versions, and plugin configurations.


## Architectural Structure

The project is organized into three distinct modules to demonstrate dependency management and modularity:

### 1. Core Data Structures (`/list`)

The foundation layer. It contains custom implementations of basic data structures.

- **Responsibility**: Provide low-level storage and retrieval mechanisms.
- **Dependencies**: None.
- **Key Component**: `LinkedList` - A custom singly linked list implementation for `String` elements.

### 2. Utility Layer (`/utilities`)

The logic layer. It provides high-level operations that operate on the core data structures.

- **Responsibility**: Implement reusable business logic (e.g., string manipulation).
- **Dependencies**: Depends on the `list` module.
- **Key Components**:
  - `StringUtils`: A facade providing a simplified API for splitting and joining.
  - `SplitUtils` & `JoinUtils`: Internal implementations of the split/join logic.

### 3. Application Layer (`/app`)

The orchestration layer. It serves as the entry point and coordinates the flow between utilities and data structures.

- **Responsibility**: Define the application workflow and handle output/UI (console).
- **Dependencies**: Depends on both `utilities` and `list`.
- **Key Component**: `App` - The main execution class.

## Dependency Flow

The project follows a strict hierarchical dependency graph to prevent circular dependencies and ensure maintainability:

`app` $\rightarrow$ `utilities` $\rightarrow$ `list`

## Design Patterns Used

- **Facade Pattern**: `StringUtils` acts as a facade to hide the complexity of `SplitUtils` and `JoinUtils` from the application layer.
- **Layered Architecture**: Clear separation between data structures, utility logic, and the application entry point.
- **Singly Linked List**: A basic implementation of a linear data structure to demonstrate manual memory/pointer management in Java.
