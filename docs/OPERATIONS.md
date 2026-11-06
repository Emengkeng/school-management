# Self-hosting and operational runbook

Planned runbook; no deployment files or production services exist yet. Do not run guessed commands from this document. Step 01 adds tested local commands; step 13 adds a tested VPS procedure and actual configuration references.

## Local development target

One reproducible dependency lockfile, supported Node LTS, supported PostgreSQL major, environment example containing placeholders only, SQL migrations and synthetic fixtures for two schools. Docker Compose may run local PostgreSQL and scanning/mail test tools; app and worker can run locally. Document install, migrate, seed, app, worker and test commands only after they exist. Use production-like non-owner DB credentials for isolation tests.

## VPS deployment order

1. Provision a maintained Linux host; set SSH key access, firewall, time sync and security updates. Define operator/recovery contacts.
2. Configure DNS for platform and school subdomains. Keep custom domains inactive until ownership/TLS verification passes.
3. Install a supported container runtime/Compose or an equivalently documented process supervisor. Use one approach consistently.
4. Provision private PostgreSQL access, restricted runtime/worker roles, separate migration role, private upload/quarantine volumes and sufficient disk capacity.
5. Configure reverse proxy HTTPS, trusted headers, request/upload limits, allowed host routing and SSE streaming/timeouts. Only HTTPS/SSH are publicly reachable as required; DB and worker are private.
6. Load secrets through protected runtime configuration. Never bake secrets into images or expose them as NEXT_PUBLIC values.
7. Take/verify backup before migrations; deploy compatible migration, then app and worker artifacts built from a pinned revision.
8. Verify readiness, tenant routing, login, SSE, upload/download, job processing and backup destinations using synthetic data.
9. Run the pilot checklist and perform an actual restore drill before real school records are accepted.
10. Record deployed revision, migration version, operator and validation evidence. Roll back application only when database compatibility permits.

Single VPS is a cost-conscious starting point with a single-host failure risk. Keep encrypted backups off that host. Additional storage/server infrastructure for disaster recovery is necessary even when avoiding managed application services. A domain registrar/DNS service and certificate authority remain infrastructure dependencies; no architecture eliminates every external dependency.

## Health and monitoring

Separate liveness from readiness. Track DB pool/query latency, disk space/inodes, storage growth, worker heartbeat, oldest outbox/job age, retries/dead letters, SSE connection counts, error rate, certificate expiry and reconciliation failures. Use structured logs with request/tenant/job IDs and no sensitive content. Set alert thresholds from pilot measurements, record actual recipients and verify alert delivery. A busy school must not exhaust all worker capacity.

## Backups and recovery

Proposed pilot targets: recovery point at most 24 hours and recovery within 8 hours; these are targets to validate, not guarantees. Before online collection, require more frequent DB protection/PITR and reconciliation of external transactions after recovery.

Back up PostgreSQL, uploaded files, generated reports, critical configuration and safely escrowed encryption/recovery secrets. Keep keys separately from encrypted backups. A DB-only backup does not recover documents. Use documented retention, encryption, integrity verification and off-host copies. Test a consistent DB/file recovery boundary; incomplete uploads must be recoverable or cleaned safely.

Restore drill: restore into an isolated environment with outbound notifications/payments disabled; validate both synthetic tenants, RLS, files, reports, balances, roles and outbox state. Do not replay delivered messages or charges. Reconcile provider records after the backup point before reopening finance. A per-school export is not a replacement for disaster backup; restoring one school from a shared database needs scoped extraction and relationship checks.

## Incident procedures

| Incident | First response and recovery |
|---|---|
| DB outage | Fail mutations safely; preserve pending client intent; restore DB before workers resume |
| Disk exhaustion | Stop new uploads/exports, alert, recover capacity; never delete historical records blindly |
| Worker outage | Records remain committed; restart with lease recovery and idempotent effects |
| Suspected tenant leak | Restrict affected capability, preserve evidence, revoke implicated sessions; assess scope and required notifications |
| Domain/TLS failure | Use platform subdomain; repair verification/certificate without changing tenant ownership |
| Failed deployment | Halt rollout, assess schema compatibility, recover from documented backup when required |
| Provider outage | Show pending/unavailable, retain events, use reconciliation; no fabricated confirmation |

## Document converter and growth

Run DOCX-to-PDF processing in a restricted self-hosted converter with pinned fonts, network/file isolation, CPU/memory/time limits and job-specific temporary profiles. Monitor failed jobs and artifact validation, clean temporary files, and back up school-approved template versions/manifests. Isolate heavy report queues from attendance alerts. See REPORT-CARDS.

Before multiple app nodes, provide shared private storage, bounded database pools, session/config consistency and correct cross-instance event fanout. SCALE defines staged growth; the initial VPS is not a million-concurrent-user claim.

## Capacity and maintenance

Measure concurrent users, SSE connections, upload sizes, report generation CPU and database growth before promising capacity. Schedule reports/scans with bounded concurrency. Recheck dependency security regularly. Document credential rotation, staff access review and certificate renewal tests. Upgrade one supported layer at a time with staging evidence. Multi-instance deployment later requires shared private storage, cache/version coordination, compatible sessions and cross-instance durable event delivery.
