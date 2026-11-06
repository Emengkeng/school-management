# Architecture

## Baseline decisions

Use a modular monolith: Next.js App Router and TypeScript for the application/API; PostgreSQL for durable records, sessions, jobs and event history; a separate Node worker process from the same repository; a reverse proxy for HTTPS/routing; private filesystem storage behind a storage adapter on the first VPS.

No managed authentication, realtime, database, queue or storage service is required. Email delivery, mobile push and online payment networks may require external delivery infrastructure. Keep these adapters optional; in-app notifications and manual fee recording work without them. Do not build cryptography or a payment processor.

At implementation, use a maintained self-hostable authentication library with local database sessions. Proposed candidates must be evaluated for maintenance, security, licensing, recovery and multiple-host behaviour before adoption. Use Drizzle plus a PostgreSQL driver as the planned typed persistence/migration layer; preserve direct SQL access for constraints, RLS and transactions. Pin supported versions in step 01, not in this planning commit.

## Runtime boundaries

| Component | Responsibility |
|---|---|
| Reverse proxy | HTTPS, verified host routing, request limits, streaming without buffering |
| Next.js Node process | UI, authenticated HTTP mutations/queries, SSE subscriptions, download authorisation |
| PostgreSQL | Records, constraints, row security, audit, transactional outbox and durable jobs |
| Worker | Outbox dispatch, retries, scheduled reminders, scanning, exports, reports and reconciliation |
| Private storage | Quarantined/approved uploads and generated artifacts outside the public directory |

Start with one application instance and one worker on a VPS. Redis, microservices, Kubernetes, Elasticsearch and dedicated databases per school are not baseline dependencies. Add components only against measured limits and document the migration.

## Proposed code boundaries (not yet created)

`src/app`: routes and rendering. `src/modules/<domain>`: server-only services, policies, validation, repositories and tests. `src/platform`: identity, tenancy, entitlements, storage, audit, jobs, notifications. `src/db`: schema and migrations. `src/worker`: worker entrypoint and handlers. Shared UI must not import server secrets or unrestricted repositories.

Route handlers and Server Actions remain thin: authenticate → resolve verified tenant/membership → authorise feature and action → validate → domain service/transaction → return a minimal response. Recheck every entry point; layouts and menu hiding are not security boundaries.

## Write transaction

1. Resolve actor and tenant from trusted server context; validate the target relationship.
2. Check effective capability, expected record version and idempotency key where applicable.
3. In one short transaction set local tenant/actor context, write business changes, audit metadata and an outbox event.
4. Commit before acknowledging success. Do not call payment/email providers inside a DB transaction.
5. Worker dispatches the committed event with bounded retries and stable identifiers.
6. Clients use the notification as a signal to retrieve current authorised data.

Use at-least-once processing with idempotent handlers, not an exactly-once claim. Outbox/jobs have availability time, lease expiry, attempts, last error, dead-letter state and unique effect keys. Recover expired leases after crashes; do not hold DB transactions open during network work. `LISTEN/NOTIFY` may wake consumers but is not durable storage.

## Realtime and offline

Use authenticated Server-Sent Events for server-to-client invalidations; HTTP for mutations. Every event has a durable cursor and a minimal audience-safe payload. Authorise each connection and topic; periodically revalidate membership, session and flags. Revocation terminates access; sensitive queries always recheck it.

Use heartbeats, bounded replay and connection limits. On reconnect replay visible events from the cursor; if history expired, request a complete authorised resync. Never send a tenant-wide payload and rely on client filtering. Polling is a fallback. Verify proxy buffering/timeouts during deployment.

Persist permitted offline drafts/actions in browser storage with tenant, actor, operation ID, record version and local event time. Session expiry pauses sync; another user or school must not inherit pending work. Server receipt time is authoritative for receipt, while reported event time is retained separately. Resolve conflicts visibly. Online-only: payments/refunds, permissions, marks publication and custom-domain activation. Offline student data is minimised and cleared on logout where feasible; a shared device cannot guarantee confidentiality of downloaded files.

## Caching and files

Default private student/finance queries to uncached responses. Never place authenticated responses in shared public caches. Any later cache key must include tenant, audience and relevant permission/version context. Begin without cross-request caching for sensitive records.

Store uploads with generated identifiers, school ownership, content hash, MIME, size and scan status. All preview/download routes enforce current access. Use a storage interface so local disk can later migrate to self-hosted or external S3-compatible storage. No public bucket or `public/uploads` for student material.

## Decision sources

Consult current official versions when implementing:
- https://nextjs.org/docs/app/guides/self-hosting
- https://nextjs.org/docs/app/guides/authentication
- https://www.postgresql.org/docs/current/ddl-rowsecurity.html
- https://cheatsheetseries.owasp.org/cheatsheets/File_Upload_Cheat_Sheet.html

These support platform behaviour; the product-specific design above is a project decision.
