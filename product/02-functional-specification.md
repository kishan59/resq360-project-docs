# ResQ360 Functional Specification

## Roles

- `OWNER`: full operational control, approvals, correction privileges, settings.
- `TEAM_MEMBER`: field capture, medical logging, vet trip requests, assigned operations.

## Primary flows

## Flow 1: Field encounter intake (mobile)

1. Team member starts an encounter.
2. App captures initial media (photo or video).
3. App captures pickup location (`lat`, `lon`, timestamp, accuracy).
4. Optional: capture initial audio note.
5. Optional: assign or scan QR code.
6. Encounter is saved locally if offline and queued for sync.

Acceptance criteria:

- Encounter can be created without connectivity.
- At least one initial media item is mandatory.
- Location capture must include timestamp and accuracy.
- Sync retry is automatic when connectivity returns.

## Flow 2: Patient creation and triage (shelter)

1. Staff creates patient record linked 1:1 to encounter.
2. Staff sets temperament and cage zone.
3. Patient enters `IN_SHELTER` state.

Acceptance criteria:

- Patient cannot exist without linked encounter.
- Encounter can create at most one patient.
- Cage zone and status must use approved enums.

## Flow 3: Daily medical loop

1. Team member reviews prior logs.
2. Team member records new audio treatment note.
3. Optional transcription job runs based on org settings.
4. Log can be tagged as vet update.

Acceptance criteria:

- Audio URL is required for each medical log.
- `logged_by_id` and `patient_id` are required.
- Failed transcription does not block log creation.

## Flow 4: Vet trip lifecycle

1. Team member submits vet trip request.
2. Owner approves in command center.
3. Trip marked completed after return.

Acceptance criteria:

- Status transitions follow allowed path only.
- Scheduled date is optional at request, required before operational dispatch.
- Requester identity is always stored.

## Flow 5: Record correction and oversight

1. Owner reviews records flagged or manually inspected.
2. Owner updates incorrect values.
3. Significant changes are audit logged.

Acceptance criteria:

- Team members cannot perform owner-only corrections.
- Every correction stores actor and timestamp.

## Flow 6: Inventory arrivals

1. Owner logs inbound item name, quantity, unit, date.
2. Entries are viewable in command center history.

Acceptance criteria:

- Quantity must be positive integer.
- Arrivals are append-only in MVP (no silent overwrite).

## Business rules

- Public users do not write directly to encounter system.
- Reporter identity can be anonymous.
- Reporter deduplication uses normalized phone number when provided.
- QR collar lifecycle is reusable after patient is `RELEASED` or `DECEASED`.

## Open questions

1. Should team members be able to edit their own encounters after sync?
2. What minimum GPS accuracy is required to mark location as valid?
3. What retention period is required for audio and video artifacts?
