# ResQ360 Delivery Plan

## Current phase

Project foundation hardening and contract alignment.

## Milestones

## M0: Documentation and contract lock

Scope:

- Finalize product, architecture, and data/API contracts
- Define state transition rules
- Define role-permission matrix
- Define sync and conflict policy

Definition of done:

- Required docs exist and are approved
- Open questions reduced to prioritized list

## M1: Backend foundation

Scope:

- Auth + RBAC
- Core schema migrations (run `npx prisma migrate dev --name contract-hardening`)
- Encounter and patient CRUD with sync envelope
- Medical logs API
- Vet trip state machine
- Audit logging for protected mutations
- Soft-delete support

Definition of done:

- Protected routes enforce role checks
- Core entity APIs pass integration tests
- Sync idempotency verified (replay detection working)
- State transitions enforced (patient status, vet trip lifecycle)
- All protected writes create audit log entries

## M2: Mobile field workflow

Scope:

- Offline encounter creation
- Media and audio capture integration
- Sync queue and retry behavior

Definition of done:

- Offline-to-online flow verified on real device

## M3: Command center workflow

Scope:

- Vet trip approvals
- Patient status management
- Inventory arrivals logging
- Owner correction tools + audit visibility

Definition of done:

- Owner can run complete daily ops workflow

## M4: Public portal and donations

Scope:

- Public story pages
- Donation integration

Definition of done:

- Donation flow tested end-to-end in staging

## M5: Optional AI enhancement

Scope:

- Async transcription
- Visual fallback matching with human confirmation

Definition of done:

- AI results are non-blocking and operationally safe

## Risks and mitigations

1. Risk: Offline sync conflicts corrupt user trust.
Mitigation: Contract-first sync design and conflict reporting.

2. Risk: Storage overrun due to media-heavy operations.
Mitigation: Compression policy and upload constraints.

3. Risk: Ambiguous status transitions.
Mitigation: Explicit state machine and server-side validation.

## Next 3 priorities

1. Execute Prisma migration: `npx prisma migrate dev --name contract-hardening` to apply schema changes to database.
2. Mobile backend integration testing: validate endpoints against mobile client sync requirements.
3. Audit trail UI: design read-only view for command center to display change history.
