# ResQ360 Decision Log

This file records significant product and technical decisions.

## Entry template

- Date:
- Decision:
- Context:
- Options considered:
- Chosen option:
- Rationale:
- Consequences:
- Follow-up actions:

## Decisions

## 2026-04-21 - Scripted Android run workflow for Windows path reliability

- Context: Running `expo run:android` from long workspace paths on Windows caused Gradle/path failures and repeated need to recreate `android/local.properties`.
- Options considered: Keep manual workaround (move project to root drive); commit `android/local.properties`; add scripted workflow that automates short-path execution and SDK config.
- Chosen option: Add scripted workflow (`npm run android:dev`) that runs prebuild, writes `android/local.properties`, and maps a short temporary drive path when needed.
- Rationale: Removes repetitive manual fixes, keeps repository clean (no committed machine-specific local.properties), and stabilizes local developer setup.
- Consequences: Team should prefer wrapper script over raw `expo run:android` on Windows.
- Follow-up actions: Monitor whether `newArchEnabled` + WatermelonDB introduces native build issues and adjust app.json if needed.

## 2026-04-20 - Sync idempotency via clientMutationId

- Context: Offline-first mobile clients may retry mutations after network failure, risking duplicate operations.
- Options considered: Accept duplicates and deduplicate on read; implement server-side replay detection; client-enforced uniqueness with timeouts.
- Chosen option: Server-side replay detection with `SyncMutation` table storing `clientMutationId` and cached response.
- Rationale: Survives client crash/reinstall, reduces mobile complexity, audit trail is clean.
- Consequences: All write endpoints must accept sync metadata; cached responses returned on replay without re-execution.
- Follow-up actions: Document sync payload contract in API specs; ensure mobile client generates UUIDs for all mutations.

## 2026-04-20 - Explicit state machines for patient and vet trip

- Context: Ambiguous status transitions (e.g., can RELEASED patient go back to IN_SHELTER?) caused confusion and bugs.
- Options considered: Document transitions informally; hardcode transition rules in code; define as configuration.
- Chosen option: Define transition graphs as constants in controllers and validate on every write.
- Rationale: Self-documenting code, impossible to bypass, terminal states immutable by definition.
- Consequences: New status types require code change (not data-driven); rejections are strict and non-negotiable.
- Follow-up actions: Document state diagrams in architecture doc; provide clear error messages on invalid transitions.

## 2026-04-20 - Phone normalization to E.164 for reporter deduplication

- Context: Reporters may submit encounters with phone numbers in different formats ("+1 555-0123", "555-0123", "15550123"); deduplication was failing.
- Options considered: Fuzzy matching; strict format enforcement; normalize all to E.164 on ingest and deduplicate.
- Chosen option: Normalize to E.164 (+ prefix, no spaces/dashes) on ingest; store in unique `phone_e164` field; reject malformed input.
- Rationale: E.164 is international standard; deduplication becomes trivial unique constraint; validation is simple regex.
- Consequences: International numbers supported; US-formatted numbers parsed; invalid formats rejected clearly.
- Follow-up actions: Document E.164 format in API contract; provide clear error when phone normalization fails.

## 2026-04-20 - Audit logging for all owner corrections

- Context: Owner corrections to sensitive fields (pickup location, terminal patient states) could be disputed; no trail of who changed what.
- Options considered: Log in separate service; inline audit on entity (version history, update_reason); immutable AuditLog table.
- Chosen option: Immutable `AuditLog` table recording before/after JSON, actor, action, timestamp.
- Rationale: Immutable (append-only) prevents tampering; JSON snapshots enable rich diffs; decoupled from entity storage.
- Consequences: Audit is never deleted (even if entity is soft-deleted); adds latency to protected writes; separate table to maintain.
- Follow-up actions: Design audit UI for command center; ensure audit table is backed up separately; document auditable actions in API contract.

## 2026-04-20 - Soft-delete for data retention compliance

- Context: Animal welfare NGO may need to retain records for compliance even after entity deletion (e.g., deceased animal for memorial).
- Options considered: Hard delete with archival snapshots; soft-delete with deleted_at flag; partition table after expiry.
- Chosen option: Soft-delete with `deleted_at` timestamp on Patient, Encounter, MedicalLog, VetTrip, InventoryArrival.
- Rationale: Queries exclude deleted by default; retention windows easy to enforce; audit trail preserved; restores possible if needed.
- Consequences: Deleted records remain in database (storage cost); must exclude deleted_at in all queries (N+1 risk); restore logic needed.
- Follow-up actions: Add cascade logic for patient soft-delete (deletes medical logs, vet trips); implement retention expiry policy; document restore workflows.

## 2026-04-20 - Markdown docs as source of truth

- Context: Initial planning artifacts existed in PDF/Google Docs format.
- Options considered: Keep docs in external tools; move all live docs into repository.
- Chosen option: Keep live docs in repository under `project-docs/` as Markdown.
- Rationale: Version control, traceability, and AI accessibility in coding sessions.
- Consequences: All future requirement and architecture updates must be reflected in repo docs.
- Follow-up actions: Keep PDFs as archive only.

## 2026-04-20 - Contract-first project governance

- Context: Project experienced drift due to AI session context fragmentation.
- Options considered: Continue code-first iteration; enforce contract-first approach.
- Chosen option: Contract-first implementation.
- Rationale: Reduces ambiguity and keeps implementation aligned across sessions and contributors.
- Consequences: Feature work begins only after doc alignment.
- Follow-up actions: Update docs before non-trivial implementation.
