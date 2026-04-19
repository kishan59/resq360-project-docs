# ResQ360 Data and API Contract

## Core entities

- Users
- Reporters
- Encounters
- Patients
- PatientMedia
- MedicalLogs
- VetTrips
- InventoryArrivals
- AppSettings
- **SyncMutation** (idempotency and replay detection)
- **AuditLog** (immutable audit trail for protected mutations)

## Enum definitions

## UserRole

- `OWNER`
- `TEAM_MEMBER`

## PatientStatus

- `IN_SHELTER`
- `AT_EXTERNAL_VET`
- `RELEASED`
- `DECEASED`

## VetTripStatus

- `PENDING_APPROVAL`
- `APPROVED`
- `COMPLETED`

## Temperament

- `CALM`
- `VIOLENT`

## CageZone

- `DANGER`
- `MEDIUM`
- `MINOR`
- `FREE_ROAMING`

## Role-permission matrix (MVP)

Legend:

- `Y`: allowed
- `N`: not allowed
- `Y*`: allowed with server-side field restrictions

| Capability | OWNER | TEAM_MEMBER | Notes |
| --- | --- | --- | --- |
| Login and view own profile | Y | Y | `/auth/login`, `/auth/me` |
| Create encounter | Y | Y | Offline creation allowed |
| Edit own unsynced encounter | Y | Y | Client-side until first successful sync |
| Edit synced encounter non-critical fields | Y | Y* | Team can edit only notes/media metadata; not pickup location |
| Edit pickup location fields | Y | N | Owner-only correction with audit log |
| Create patient from encounter | Y | Y | Strict one-to-one enforced |
| Update patient temperament/cage zone | Y | Y | Both roles allowed |
| Update patient status | Y | Y* | Team cannot set terminal status without reason note |
| Set patient status to `RELEASED` | Y | N | Owner-only in MVP |
| Set patient status to `DECEASED` | Y | N | Owner-only in MVP |
| Add medical logs | Y | Y | Audio URL required |
| Edit or delete medical logs | Y | N | Soft delete preferred |
| Create vet trip request | Y | Y | Requester ID required |
| Approve vet trip | Y | N | Owner-only |
| Complete vet trip | Y | Y* | Team can mark complete only if trip is already approved |
| Create inventory arrival | Y | N | Owner-only in MVP |
| View inventory arrivals | Y | Y | Read access for operations visibility |
| Update app settings | Y | N | Owner-only |
| View reporter identity fields | Y | Y* | Team view allowed only when needed for active encounter |
| Run correction workflows (protected fields) | Y | N | Owner-only + audit log required |

## State transition rules

## PatientStatus transitions

- `IN_SHELTER` -> `AT_EXTERNAL_VET`, `RELEASED`, `DECEASED`
- `AT_EXTERNAL_VET` -> `IN_SHELTER`, `RELEASED`, `DECEASED`
- `RELEASED` -> no further transitions in MVP
- `DECEASED` -> no further transitions in MVP

## VetTripStatus transitions

- `PENDING_APPROVAL` -> `APPROVED`
- `APPROVED` -> `COMPLETED`
- `COMPLETED` -> terminal

## Data integrity constraints

1. `Encounter` to `Patient` is strict one-to-one.
2. `Patient` must reference exactly one `Encounter`.
3. `MedicalLog` requires `patient_id`, `logged_by_id`, `audio_log_url`.
4. `InventoryArrival.quantity` must be greater than 0 and an integer.
5. Reporter phone should be normalized to E.164 when present and stored in `phone_e164` (unique).
6. All write endpoints require sync metadata: `clientMutationId`, `deviceId`, `baseVersion`.
7. All records have `created_at` and `updated_at`; soft-deletable records also have `deleted_at`.

## Protected fields (owner-only writes)

These fields are immutable by team members and can only be corrected by OWNER with audit logging:

- `Encounter.pickup_lat`, `pickup_lon`, `pickup_accuracy_m`, `pickup_captured_at`
- `Patient.status` (only terminal states `RELEASED`, `DECEASED`)
- `AppSettings.*`
- `InventoryArrival` (create-only, no updates)

## Audit trail

Every protected write creates an immutable `AuditLog` record:

```json
{
  "actor_id": "user-id",
  "entity_type": "Patient",
  "entity_id": "patient-id",
  "action": "UPDATE_PATIENT_STATUS",
  "before_json": { /* snapshot before change */ },
  "after_json": { /* snapshot after change */ },
  "created_at": "2026-04-20T..."
}
```

## Soft-delete behavior

Sensitive entities support soft-delete via `deleted_at` field:

- Set `deleted_at` on delete (do not remove record)
- Queries exclude soft-deleted records by default
- Audit logs remain even after entity soft-delete

## API baseline (MVP)

## Auth

- `POST /auth/login`
- `GET /auth/me`

## Encounters

- `POST /encounters`
- `GET /encounters/:id`
- `PATCH /encounters/:id` (owner-only for protected corrections)

## Patients

- `POST /patients`
- `GET /patients/:id`
- `PATCH /patients/:id`
- `PATCH /patients/:id/status`

## Medical logs

- `POST /patients/:id/medical-logs`
- `GET /patients/:id/medical-logs`

## Vet trips

- `POST /patients/:id/vet-trips` (team member or owner)
- `GET /vet-trips/approved` (returns approved trips ordered by `scheduled_date`)
- `PATCH /vet-trips/:id/status` (owner-only state transitions; requires `scheduled_date` when moving to `APPROVED`)
- `PATCH /vet-trips/:id/approve` (owner-only, convenience endpoint, sets status to `APPROVED`)
- `PATCH /vet-trips/:id/complete` (owner can always, team can only if already `APPROVED`)

## Inventory arrivals

- `POST /inventory-arrivals` (owner-only)
- `GET /inventory-arrivals` (all authenticated users)

## Error response format

```json
{
  "status": "error",
  "message": "Human-readable message",
  "code": "VALIDATION_ERROR",
  "details": []
}
```

Common status codes:
- `200`: Success (applied or successfully synced)
- `400`: Validation error (bad input, invalid transition, missing required fields)
- `401`: Unauthorized (missing/invalid JWT)
- `403`: Forbidden (insufficient role permissions)
- `409`: Conflict (sync conflict, version mismatch)

## Sync conflict policy (MVP)

## Sync payload contract

Every write request (POST/PATCH to entity endpoints) must include a top-level `sync` object:

```json
{
  "sync": {
    "clientMutationId": "uuid-string",
    "deviceId": "mobile-device-id",
    "baseVersion": "2026-04-20T12:34:56Z",
    "changedFields": ["temperament", "cage_zone"],
    "changedAtClientTs": "2026-04-20T12:35:00Z"
  },
  "temperament": "CALM",
  "cage_zone": "DANGER"
}
```

Required fields:
- `sync.clientMutationId` - UUID to prevent replay on retry
- `sync.deviceId` - Mobile device identifier
- `sync.baseVersion` - Last known `updated_at` of entity (for update operations; null for create)

Optional fields:
- `sync.changedFields` - Array of field names that changed (helps track audit)
- `sync.changedAtClientTs` - Timestamp when user made the change locally

## Server response per mutation

All write endpoints return a structured response:

```json
{
  "status": "applied",
  "message": "Patient updated successfully",
  "data": { /* entity object */ },
  "sync": {
    "status": "applied",
    "entityId": "server-id",
    "serverVersion": "2026-04-20T12:35:01Z",
    "conflictFields": [],
    "resolution": "applied"
  }
}
```

Response fields:
- `status`: `applied` | `rejected` | `conflict`
- `data`: Updated or created entity object
- `sync.status`: `applied` | `rejected` | `conflict`
- `sync.entityId`: Server ID (mapped if client provided UUID)
- `sync.serverVersion`: Server `updated_at` after mutation
- `sync.conflictFields`: Array of field names in conflict (if status=`conflict`)
- `sync.resolution`: `applied`, `server_wins`, `owner_wins`, `manual_review`

## Conflict resolution order

1. Idempotency check by `clientMutationId`.
2. Reject invalid transitions or schema violations.
3. For protected fields (`pickup_lat`, `pickup_lon`, terminal statuses), owner writes win.
4. For non-protected fields, apply latest valid write by server timestamp.
5. If same field updated by owner correction and team update, owner correction wins.
6. If conflict cannot be auto-resolved safely, return `manual_review` and keep server value unchanged.

## Client behavior on conflict

1. Mark local item as conflicted.
2. Pull latest server snapshot for entity.
3. Show field-level conflict details to user.
4. Allow retry with updated `baseVersion` after user confirmation.

## Additional sync safeguards

- Sync must be retry-safe and idempotent: same `clientMutationId` submitted twice returns cached response without double-mutation.
- Mutations are processed in order per entity when possible; out-of-order mutations may conflict.
- Terminal state transitions (`RELEASED`, `DECEASED`) are immutable in MVP: once set, cannot be changed.
- Every owner correction to protected fields creates an audit log entry with before/after JSON snapshots.
- Version conflict detection: if `baseVersion` does not match server `updated_at`, the client had stale data; resolution policy applies (owner wins).
- Invalid state transitions are rejected immediately with `status: 'rejected'`.
