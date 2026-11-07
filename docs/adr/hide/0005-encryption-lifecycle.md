# ADR-005: Encryption Lifecycle and Implementation

The use of a reference implementation for symmetric encryption.
The "Minimum Decryption" principle: data remains encrypted unless a transformation between layers (DTO $\leftrightarrow$ Repository) requires it.
The separation of transit and storage keys.
