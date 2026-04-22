# ResQ360 Changelog

## 2026-04-21 - Mobile stabilization pass (contract alignment + Android tooling)

### Mobile contract and sync alignment
- Reworked mobile API service to use backend-aligned endpoints (`/api/auth/login`, `/api/auth/me`, `/api/encounters`, `/api/upload`)
- Removed invalid upload token usage and switched to secure token retrieval from `expo-secure-store`
- Added robust JSON response parsing and centralized auth token handling in mobile service layer
- Added client sync metadata utility for `clientMutationId` and persistent `deviceId`

### Offline data model hardening
- Upgraded WatermelonDB schema from v2 to v3
- Added local rescue fields required by backend contract and sync lifecycle:
	- `pickup_accuracy_m`, `pickup_captured_at`
	- `client_mutation_id`, `device_id`, `base_version`
	- `server_encounter_id`, `last_sync_error`
- Added explicit WatermelonDB migration file and wired migrations into database adapter

### Intake and sync flow fixes
- Updated Rescue Intake save flow to persist location accuracy and capture timestamp
- Persisted sync identifiers at intake creation so retries are idempotent
- Rebuilt sync engine payload mapping to match local model and backend contract
- Added partial-failure handling (`sync_failed`) with local error persistence and improved user feedback

### Auth bootstrap hardening
- Added token validation on app boot via `/auth/me`
- Purges stale/invalid token from secure storage to avoid broken ghost sessions

### Android build/run reliability on Windows
- Added `npm run android:dev` wrapper script for reliable Android install from long Windows paths
- Script automatically:
	- runs `expo prebuild --platform android`
	- generates `android/local.properties` from detected SDK path
	- uses temporary `subst` drive mapping when project path is long
- Added `npm run android:prepare` utility to generate `android/local.properties` manually

### Docs and developer workflow updates
- Updated mobile README with env-based API config (`EXPO_PUBLIC_API_BASE_URL`)
- Added documented Android-safe workflow replacing repeated manual local.properties edits

## 2026-04-20 - Backend hardening pass

### Prisma schema
- Added `Temperament` enum (CALM, VIOLENT) with validation
- Added `phone_e164` field to Reporter for E.164 normalized deduplication
- Added `SyncMutation` model for idempotent replay detection (`clientMutationId`, `deviceId`, `entity_type`, `entity_id`, `response_json`)
- Added `AuditLog` model for immutable audit trail (`actor_id`, `entity_type`, `entity_id`, `action`, `before_json`, `after_json`)
- Added `updated_at` timestamp to all entities
- Added `deleted_at` soft-delete field to Patient, Encounter, MedicalLog, VetTrip, InventoryArrival for retention

### Backend controllers
- **Auth**: Created `src/routes/authRoutes.js` with POST /auth/login and GET /auth/me
- **Encounter**: Added sync envelope, phone normalization, owner-only field protection (pickup_lat, pickup_lon, pickup_accuracy_m, pickup_captured_at), getEncounterById(), updateEncounter()
- **Patient**: Added state machine for status transitions, sync envelope, getPatientById(), updatePatient(), updatePatientStatus() with owner-only terminal states (RELEASED, DECEASED)
- **VetTrip**: New module with state machine, sync envelope, approveVetTrip(), completeVetTrip(), getApprovedTrips()
- **MedicalLog**, **Inventory**, **Settings**: Added sync envelope and audit logging

### Utilities
- Created `src/utils/auth.js` - JWT secret retrieval with fail-fast if env var missing
- Created `src/utils/phone.js` - Phone normalization to E.164 standard
- Created `src/utils/audit.js` - Audit log creation helper wired into all protected mutations
- Created `src/utils/sync.js` - Sync envelope handling, idempotency checking, version conflict detection

### Routes
- Expanded all entity routes to include GET /:id and PATCH /:id
- Added PATCH /:id/status for patient and vet-trip
- Added POST /:id/medical-logs nested under patients
- Added GET /vet-trips/approved endpoint
- Added restrictTo('OWNER') to inventory POST and vet-trip status change routes
- Added /auth/login and /auth/me to authRoutes

### Role-based access control
- Inventory creation now owner-only
- Patient terminal status changes (RELEASED, DECEASED) now owner-only
- Vet trip approvals now owner-only; completions available to team if already approved
- Encounter pickup location corrections (pickup_lat, pickup_lon, pickup_accuracy_m, pickup_captured_at) now owner-only

### Validation
- Encounter: Phone number normalization and E.164 storage
- Patient: Temperament and cage_zone enum validation, status transition graph
- VetTrip: Status transition graph, required scheduled_date when moving to APPROVED
- Inventory: Positive integer validation for quantity

### Comment cleanup
- Removed unnecessary decorative/redundant comments
- Retained concise, implementation-critical documentation
- All auth middleware comments removed in favor of code clarity

### Startup validation
- Server now fails loudly at startup if JWT_SECRET env var is missing
- Clear error message guides configuration

## 2026-04-20

- Bootstrapped documentation hub in `project-docs/` with product, architecture, delivery, decision, and AI operations files.
- Added first-pass vision, functional flows, architecture baseline, and data/API contract.
- Added initial milestone plan and risk register.
- Renamed documentation folder from `docs/` to `project-docs/`.
- Added multi-repo documentation placement policy for separate backend/mobile repositories.
- Added role-permission matrix to data/API contract.
- Added detailed sync payload contract and conflict resolution policy.
