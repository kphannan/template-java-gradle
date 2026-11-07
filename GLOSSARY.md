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
