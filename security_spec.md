# Security Specification for 错题举一反三打印机

## Data Invariants
1. A `WrongQuestion` record MUST belong to a valid authenticated user (`userId`).
2. A user can ONLY read, create, update, or delete their own `WrongQuestion` records.
3. `createdAt` must be set at creation and remain immutable.
4. `userId` must match the authenticated user's UID and remain immutable.
5. `variations` is an array of objects, must have a reasonable maximum size to prevent resource exhaustion.

## The "Dirty Dozen" Payloads (Red Team Test Cases)
1. **Identity Spoofing (Create)**: Attempt to create a record with a `userId` that does not match `request.auth.uid`.
2. **Identity Spoofing (Update)**: Attempt to change the `userId` of an existing record.
3. **Data Poisoning (ID)**: Attempt to use a 2MB string as a document ID.
4. **Data Poisoning (Field)**: Inject a 1MB string into the `knowledgePoint` field.
5. **Unauthorized Read**: User A attempts to read User B's record.
6. **Unauthorized Delete**: User A attempts to delete User B's record.
7. **Shadow Field Injection**: Attempt to add an `isAdmin: true` field to a `WrongQuestion` record.
8. **Immutability Breach**: Attempt to change the `createdAt` timestamp.
9. **Resource Exhaustion (Array)**: Attempt to save 1,000 variations in a single document.
10. **Type Mismatch**: Send a boolean for the `originalQuestion` field.
11. **Orphaned Record**: (N/A for this simple schema as there are no parent/child collection relationships yet, but we'll ensure `userId` is valid).
12. **Malformed JSON in ID**: Using special characters in document IDs that could bypass filters.

## Implementation Steps
1. Define global helpers.
2. Define `isValidWrongQuestion` with strict key checks.
3. Implement action-based updates (e.g., updating variations vs updating original details).
