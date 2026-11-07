# ADR 005: Encryption Lifecycle and Implementation

## Status

Proposed

## Context

Encryption is only as strong as the management of its keys. We need a defined lifecycle for encryption keys to prevent compromise and ensure long-term security.

## Decision

We will implement the following encryption lifecycle:

- **Key Generation:** Use cryptographically secure random number generators (CSPRNG).
- **Key Storage:** Keys will be stored in a dedicated Key Management Service (KMS) or a secure Vault (e.g., HashiCorp Vault), never in source control.
- **Key Rotation:** Automated rotation of keys every 90 days (or upon suspected compromise).
- **Key Revocation:** Defined process for immediately revoking compromised keys.
- **Implementation:** Use standard libraries (e.g., Node.js `crypto` module) rather than custom encryption implementations.

## Consequences

- **Positive:** Significantly reduced risk of long-term key compromise and improved security posture.
- **Negative:** Increased operational complexity in managing the KMS/Vault infrastructure.
