# ResQ360 Product Requirements Document

## Document purpose

This document is the product-level blueprint for ResQ360. It describes the complete vision, scope, user roles, system behavior, major workflows, technical foundation, and future modules for the full platform.

This PRD is written for both technical and non-technical readers. It is meant to answer:

- What is ResQ360?
- Who is it for?
- What will the full system eventually include?
- How do the mobile app, backend, admin panel, public website, and future AI tools fit together?
- What rules must the product follow so the team builds consistently?

## 1. Executive summary

ResQ360 is an offline-first rescue operations platform for street animal rescue organizations. It helps teams capture rescues in the field, preserve media and location evidence, sync data later when internet is available, manage shelter intake and patient records, support medical and vet trip workflows, and provide an audit-friendly operational backbone.

The product is designed for real-world rescue conditions where teams often work with weak connectivity, limited time, low literacy inputs, and a need for trustworthy records. The system is built around mobile-first field capture, a secure backend, structured data, role-based access control, and future expansion into an admin command center, public-facing website, and optional AI-assisted workflows.

## 2. Product vision

ResQ360 digitizes end-to-end street animal rescue operations so a rescue organization can move from field discovery to shelter intake, treatment, oversight, reporting, and public trust-building in one connected system.

The long-term vision is for ResQ360 to become the full operating system for the rescue organization:

- Field team captures encounters quickly, even offline.
- Shelter and command-center users manage patients, vet trips, inventory, and corrections.
- Owners maintain oversight, permissions, and audit visibility.
- Public users can learn about the organization and support its work through a separate public website.
- Optional ML or AI scripts can later assist with transcription, tagging, triage support, or analytics, but only after human-safe workflows are in place.

## 3. Problem statement

Animal rescue operations are often managed through a mixture of paper notes, messaging apps, voice memos, spreadsheets, and memory. That creates problems:

- Rescue information is lost or incomplete.
- Media evidence is disconnected from the record.
- Location data is inconsistent or missing.
- Offline field work cannot be captured reliably.
- Shelter intake and patient lifecycle tracking are hard to coordinate.
- Owners lack clean oversight and audit history.
- Public-facing communication is disconnected from the real operational record.

ResQ360 exists to solve these problems with a structured system that preserves the operational truth from first pickup through shelter lifecycle.

## 4. Product goals

The product should:

- Capture rescue encounters in the field with minimum friction.
- Work offline first and sync safely later.
- Preserve photo, video, audio, and pickup location data.
- Support anonymous or identified reporters.
- Let rescue staff and shelter staff manage intake and care workflows.
- Support owner oversight, correction, and reporting.
- Keep every important change auditable.
- Make future public and AI modules possible without rewriting the foundation.

## 5. Non-goals for the first release

The initial platform should not try to solve everything at once. The following are out of scope for the first pass unless explicitly added later:

- Fully autonomous AI decision-making with no human review.
- Public users writing directly into the internal rescue database.
- Fully automated inventory deduction per treatment dose.
- Complex multi-org marketplace behavior.
- Replacing human approval for owner-only actions.

## 6. Intended audience

### 6.1 Rescue field team

These are the people on the ground who find animals, capture initial media, record location, and begin the rescue process.

### 6.2 Shelter staff and command-center users

These are the people who receive animals, perform intake, manage patient status, log medical activity, and request or approve travel or treatment operations.

### 6.3 Owner / administrator

This person has full operational control, correction powers, approval rights, and settings management.

### 6.4 Public visitors

These users may only see public-facing information, stories, or organizational content. They should not be able to edit internal rescue data.

### 6.5 Future analysts or AI pipeline users

These are internal tools or scripts that may later process media, transcripts, or analytics data. They must never bypass core data integrity rules.

## 7. Product principles

ResQ360 should be designed around the following principles:

- Offline first: if the field has no internet, the app must still work.
- Human verified: important decisions should remain human-led.
- Audit friendly: important changes should leave a trace.
- Role aware: not everyone should be allowed to do everything.
- Low friction: rescue capture must be fast and simple in the field.
- Data integrity first: location, media, and lifecycle changes must be trustworthy.
- Plain language where possible: the system should be understandable to non-technical operators.

## 8. Product scope by platform

### 8.1 Mobile app

The mobile app is the field rescue tool. It should support:

- Rescue encounter capture.
- Camera capture for photos and videos.
- Audio notes.
- GPS pickup location capture.
- Anonymous or identified reporter capture.
- Offline local storage.
- Background or manual sync when internet returns.
- Intake-related screens for shelter use where appropriate.
- Simple success and failure feedback.

Mobile is the main data entry point for field team members.

### 8.2 Backend API

The backend is the source of truth for secure storage, business rules, deduplication, and role-based permissions. It should handle:

- Authentication and authorization.
- Encounter creation and updates.
- Patient creation and lifecycle changes.
- Medical logs.
- Vet trip requests and approvals.
- Inventory arrivals.
- App settings.
- Media upload references.
- Idempotent sync writes.
- Audit logging.
- Conflict detection and safe overwrite behavior.

### 8.3 Admin panel / command center

The admin panel is the future operations hub for owners and staff. It should eventually provide:

- Encounter review.
- Patient management.
- Conversion from encounter to patient.
- Vet trip management.
- Inventory management.
- Settings management.
- Audit log review.
- Correction workflows.
- Operational dashboards.

This is the place where human approval and oversight live, not the public website.

### 8.4 Public website

The public website is a separate trust-building and communication layer. It should provide:

- Public information about the organization.
- Campaigns, stories, and awareness content.
- Donation or support information if enabled.
- Contact or volunteer information.
- Possibly public success metrics or curated impact stories.

It must not write directly into internal rescue tables.

### 8.5 Future Python or ML scripts

Future scripts may perform tasks like:

- Audio transcription.
- Image tagging.
- Triage support.
- Reporting analytics.
- Trend analysis.

These tools should enrich the system, not replace the core operational record.

## 9. Primary roles and permissions

### 9.1 OWNER

The owner has full operational control. Typical permissions include:

- Approve or reject sensitive operational actions.
- Manage app settings.
- Correct important records.
- View audit trails.
- Manage users and team structure.
- Perform lifecycle changes that are restricted to owners.

### 9.2 TEAM_MEMBER

The team member is a field or shelter operator. Typical permissions include:

- Create encounters.
- Log medical notes where allowed.
- Request vet trips.
- Work with assigned operational records.
- Update allowed patient or encounter fields based on policy.

### 9.3 Public user

Public users should not have access to internal data entry or direct write paths.

## 10. End-to-end product flows

### 10.1 Field rescue intake flow

1. A team member finds an animal in the field.
2. The mobile app captures a photo or video.
3. The app captures pickup location and time.
4. The app optionally captures audio notes.
5. The app records the reporter as anonymous or identified.
6. The data is stored locally if offline.
7. The record later syncs to the backend.
8. The backend saves the encounter and related metadata.

### 10.2 Reporter flow

The reporter may be:

- Anonymous, meaning no reporter identity is stored.
- Identified, meaning the system may store and reuse their name and phone number.

If the reporter is identified, phone normalization and deduplication help avoid duplicate reporter records.

### 10.3 Shelter intake flow

1. The rescue arrives at the shelter.
2. Staff reviews the encounter.
3. The animal is converted into a patient.
4. Cage zone, temperament, and initial patient state are recorded.
5. The patient enters the shelter lifecycle.

### 10.4 Medical care flow

1. A team member opens the patient record.
2. They add a medical log or treatment note.
3. The record is stored against the patient.
4. Optional transcription or future AI tooling may later assist with interpretation.

### 10.5 Vet trip flow

1. A team member requests a vet trip.
2. The request enters an approval flow.
3. An owner or authorized operator approves it.
4. The trip is tracked through completion.

### 10.6 Audit and correction flow

1. A user makes a meaningful update.
2. The backend captures the before and after state.
3. The action is written to the audit log.
4. Owners can later review who changed what and when.

### 10.7 Sync flow

1. The mobile app stores data locally.
2. Each local record receives mutation metadata.
3. When internet is available, the app sends the record to the backend.
4. The backend checks whether the same mutation was already applied.
5. If yes, the backend returns the previous response.
6. If no, the backend applies the change and stores the sync result.

## 11. Data and domain model overview

This section explains the main data areas in plain language.

### 11.1 User

A person who can log into the system.

### 11.2 Reporter

A person who reported or witnessed an animal rescue event. This may be anonymous or identified.

### 11.3 Encounter

The first rescue record created in the field. It stores media, pickup location, reporter information, and the initial rescue event.

### 11.4 Patient

The shelter-side record for an animal that has been admitted into care.

### 11.5 Patient media

Extra media attached to a patient after intake.

### 11.6 Medical log

A medical or treatment entry for a patient.

### 11.7 Vet trip

A record of travel or hospital support related to a patient.

### 11.8 Inventory arrival

A record of new supplies entering the shelter system.

### 11.9 App settings

The organization-level settings that control feature availability and operational behavior.

### 11.10 Sync mutation

The record that helps the backend recognize whether a mobile write has already been applied.

### 11.11 Audit log

A historical record of important changes, including who made them and what changed.

## 12. Important business rules

- A rescue encounter must always capture at least one initial media item.
- Pickup location is operationally important and should be preserved.
- Anonymous reporters do not need a phone number and should not create a reporter identity record.
- Identified reporters should be deduplicated when possible.
- A patient should be linked to exactly one encounter.
- Only valid lifecycle transitions should be allowed for patient and vet trip statuses.
- Sensitive corrections should be restricted by role.
- Settings should be controlled centrally, not scattered across the app.
- Public tools should not write into internal operational tables.

## 13. Status and lifecycle concepts

### 13.1 Encounter lifecycle

An encounter begins in the field and can later be converted into a patient if the animal is admitted to the shelter.

### 13.2 Patient lifecycle

A patient moves through shelter and treatment states such as in shelter, external vet, released, or deceased.

### 13.3 Vet trip lifecycle

A vet trip may begin as pending approval, move to approved, and later complete.

## 14. Sync and reliability requirements

ResQ360 must support weak or absent connectivity. That means:

- Local capture must be available without internet.
- Mobile writes must be safe to retry.
- Duplicate submissions must not create duplicate records.
- The backend must protect against partial or repeated writes.
- Sync failures should be visible enough for operators to understand and retry.

## 15. Error handling and user experience requirements

The product should not only store errors technically. It should also present them in a usable way.

Examples:

- Permission denied should show a clear recovery path.
- Sync failure should give the user a meaningful message.
- Validation errors should explain what is missing.
- Conflict errors should explain that the server has newer data and that the user may need to reload or retry.
- Admin-only or owner-only actions should fail with a clear permission explanation.

## 16. Trust, privacy, and data protection

ResQ360 should treat sensitive operational data carefully:

- Reporter identity may be anonymous.
- Staff credentials must be protected.
- Audit history must not be casually altered.
- Public pages must not leak internal records.
- Media storage and access should be controlled.
- Future analytics or AI tools should use the minimum data required.

## 17. Technical foundation

### 17.1 Current core stack

- Mobile app: React Native
- Backend: Node.js and Express
- Database: PostgreSQL
- ORM: Prisma
- Offline mobile database: WatermelonDB
- Media storage: Cloudinary
- Authentication: JWT
- File upload: HTTP upload pipeline with stored media URLs

### 17.2 Important supporting concepts

- Offline-first local persistence
- Idempotent writes
- Sync mutation tracking
- Audit logging
- Role-based access control
- Version conflict checks
- Phone normalization for reporter deduplication

### 17.3 Future stack additions

As the product expands, the stack may also include:

- Web admin UI
- Public website framework
- Analytics tooling
- Transcription or AI services
- Scheduled jobs or scripts for data processing

## 18. Suggested architecture modules for the full platform

### 18.1 Mobile module

Handles rescue intake, local storage, sync, and field workflows.

### 18.2 API module

Handles authentication, validation, business rules, and persistence.

### 18.3 Command center module

Handles owner and shelter operations, review, corrections, and approvals.

### 18.4 Public site module

Handles external communication and trust-building.

### 18.5 Automation and AI module

Handles transcription, classification, and support tasks only after human-safe design is in place.

## 19. Future roadmap

### Phase 1: Core rescue capture

- Mobile encounter capture
- Local offline storage
- Backend encounter persistence
- Sync safety and deduplication

### Phase 2: Shelter operations

- Patient lifecycle
- Medical logs
- Vet trip management
- Owner approvals
- Audit visibility

### Phase 3: Command center and admin tools

- Web admin dashboard
- Record review and correction
- Settings management
- Operational reporting

### Phase 4: Public presence

- Public website
- Trust and storytelling pages
- Donation or outreach features

### Phase 5: Intelligent assistance

- Transcription
- Search and summarization
- Tagging and reporting assistance

## 20. Acceptance criteria for the full product vision

The product is successful when:

- Field teams can capture rescue records reliably with or without internet.
- Shelter staff can convert encounters into patients and manage care.
- Owners can oversee, correct, and audit important work.
- Public users can engage through a separate website without access to internal data entry.
- The system remains understandable, secure, and scalable as new modules are added.

## 21. Open decisions for future product planning

These items should be finalized as the system matures:

- Exact scope of the admin command center.
- Which roles may convert encounters into patients.
- The final UX for sync error visibility.
- Whether QR codes are assigned at encounter time, shelter intake time, or both.
- The exact public website content model.
- Whether AI tools are optional helpers or core product features.
- The long-term retention policy for media and audit logs.

## 22. Glossary

- Encounter: the first rescue record created in the field.
- Patient: an animal admitted into shelter care.
- Reporter: the person who reported or witnessed the rescue.
- Sync mutation: a token that prevents duplicate writes during retry.
- Audit log: a history of important changes.
- Idempotency: the same request can be safely repeated without creating duplicates.
- Conflict detection: the system notices if someone else changed a record first.
- Command center: the future admin and operations web application.
- Offline-first: the app works before internet is available.
- Public website: the external-facing site for awareness and trust-building.

## 23. Document relationship to the rest of the docs

This PRD is the umbrella document. The more detailed technical and delivery docs should continue to live in the product, architecture, delivery, and decisions folders. This PRD should stay stable enough to guide the whole project, while the lower-level docs can change as implementation details evolve.

## 24. Final note

ResQ360 is not just a mobile app and not just a backend. It is an operational system for rescue work. The job of this PRD is to describe the full product clearly enough that developers, managers, owners, and non-technical stakeholders can all stay aligned while the implementation grows over time.
