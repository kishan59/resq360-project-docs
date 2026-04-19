# ResQ360 Technical Architecture

## System components

1. Mobile app (`resq360-mobile`)
- React Native + Expo
- Offline-first local DB and sync queue
- Device integrations: location, camera, audio

2. Backend API (`resq360-backend`)
- Node.js + Express
- Prisma ORM
- JWT auth + RBAC
- Background jobs via Redis

3. Database
- PostgreSQL (primary relational store)
- PostGIS for geospatial operations

4. AI microservice (planned)
- Python + FastAPI
- Audio transcription (Whisper API)
- Visual fallback model (YOLOv8), human-verified

5. Public portal (planned)
- Next.js SSR site for public trust and donations

## Design principles

- Offline-first over always-online assumptions
- Audio-first over text-heavy workflows
- Contract-first APIs and schema evolution
- Role-bound operations with explicit permissions
- Operational safety over feature speed

## Security baseline

- JWT access tokens with role claims
- Password hashing and secure secret management
- Owner-only endpoints for correction and settings
- Server-side authorization checks on every protected route

## Sync strategy (v1)

1. Client writes locally with client-generated IDs.
2. Sync worker submits batched mutations with idempotency key.
3. Server returns per-item apply result.
4. Conflict policy:
- Owner correction wins for protected fields.
- Otherwise latest valid update by server timestamp.

## Observability baseline

- Structured API logging
- Error tracking for backend and mobile sync failures
- Job queue failure and retry metrics

## Deployment environments

- `dev`: local development
- `staging`: pre-production validation
- `prod`: live NGO operations

## Change management

- Prisma migrations required for schema changes
- Backward-compatible API changes preferred
- Breaking changes require contract update in docs first
