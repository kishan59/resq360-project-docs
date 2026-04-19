# ResQ360 Vision and Scope

## Product vision

ResQ360 digitizes end-to-end street animal rescue operations for NGOs using an offline-first, audio-first, mobile-first system.

## Target users

- NGO rescue team members
- Shelter owner and operations staff
- Public community for trust-building and donations (no internal data entry)

## Core objectives

1. Capture rescue data quickly in the field, even without internet.
2. Preserve exact pickup location for safe and compliant territory release.
3. Support low-literacy workflows using audio as primary input.
4. Coordinate shelter operations (triage, vet trips, corrections, inbound inventory).
5. Maintain a clean, auditable system that can scale by organization settings.

## Non-negotiable constraints

- Offline-first mobile capture and deferred sync
- Audio-first field workflow
- Role-based access control (`OWNER`, `TEAM_MEMBER`)
- Public website does not write to internal rescue database
- Pickup location integrity required for release operations

## Out of scope for MVP

- Public self-service dispatch forms writing into the internal database
- Fully automated per-dose inventory deduction
- Fully autonomous AI identification without human verification

## Success criteria (MVP)

1. Field team can create encounter + media + audio while offline and sync later.
2. Shelter team can create and manage patient lifecycle from encounter.
3. Vet trip requests and approvals are managed in command center.
4. Owner can correct invalid records with audit visibility.
5. Inventory arrivals can be logged with date and quantity.

## Assumptions

- Team members have Android devices with camera, mic, and GPS.
- Intermittent network is common in field conditions.
- Media storage budget is constrained and requires compression policy.
