# Scale foundation and capacity evolution

## Goal and limits

Design for a platform with millions of enrolled student records across independent schools. This is an architectural goal, not evidence of current capacity. Millions of stored students, daily active users, concurrent sessions and concurrent requests are different quantities. Never claim a supported capacity until production-like measurement demonstrates it. One VPS is the pilot deployment, not the final infrastructure for national-scale usage.

Keep Next.js, PostgreSQL and self-hostable processes as the foundation. More infrastructure will be required as load grows, but not every component is required at the first pilot. Data model, transaction boundaries, jobs, security and observability must be correct from the start.

## Workload model

Record these inputs for each benchmark: total schools/students; active enrolments; guardians per student; teaching groups; lessons/day; attendance events/student/day; peak concurrent sessions; open SSE connections; reads/writes per second; event delivery lag; report cohort size; file volume; CPU/memory/disk; DB connections; backup/restore duration. Include tenant-size skew, not only equally sized synthetic schools.

For illustration only: 1 million students × 8 lesson attendance observations × 200 school days produces 1.6 billion observations/year, before revisions. These are hypothetical planning inputs, not claims about Cameroon schedules. This makes attendance granularity, retention, indexes, partitioning and archive strategy major capacity decisions. Keep daily and per-lesson attendance configurable; never silently reduce the school's chosen tracking quality to meet capacity.

## Foundations required before growth

| Concern | Required design |
|---|---|
| Query shape | School/date-scoped indexed access, bounded page sizes and stable cursor pagination; no loading every student into memory |
| Relationships | Tenant-safe constraints, effective-dated enrolment and supported indexes; profile queries for largest tenants |
| Transactions | Short transactions, restricted DB roles, pooled connections and bounded retries; no provider/render work inside transactions |
| App state | Durable records/sessions/events in PostgreSQL; no correctness dependency on one process's memory |
| Files | Storage adapter and private access from day one; move to shared storage before adding app instances |
| Async work | Durable idempotent jobs, leases, batching and per-school fairness; separate heavy rendering from urgent delivery |
| Live delivery | Minimal scoped invalidations, bounded replay/retention, connection budgets; no broadcast of all attendance changes to every school user |
| Analytics | Bounded queries and later derived aggregates; do not calculate schoolwide rankings/finance analytics on every dashboard refresh |
| Bulk actions | Snapshot/manifest, resumability, progress, partial failures and controlled cancellation |
| Observability | Query plans, pool saturation, queue age, error/latency and tenant skew; alert without logging child content |

## Deployment stages and triggers

| Stage | Topology | Gate / trigger |
|---|---|---|
| Pilot | One VPS with proxy, app, worker, PostgreSQL, private files and off-host backups | Measure realistic school-day and report workloads; accept single-host availability limitation |
| Separate bottlenecks | Dedicated database/storage and independently sized workers; app can stay simple | Resource contention, recovery duration or sustained queue lag exceeds agreed budgets |
| Multiple app/worker nodes | Load balancer, shared private storage, shared sessions and durable event fanout; deployment/version coordination | One instance cannot meet measured peak with headroom or availability requires redundancy |
| Larger PostgreSQL estate | Tuned primary, tested standby/failover, eligible read replicas, time-based partitions and archival | Query/storage/backup maintenance metrics justify changes; test failover and replica lag |
| Tenant groups / shards | Explicit school-to-database placement, isolated large tenants where needed | Single write-primary capacity/maintenance becomes measured limit; documented migration gates |

No fixed student count automatically triggers a stage. Decide using latency, throughput, storage, availability/recovery objectives and measured headroom. Use synthetic growth tests before approaching the measured limit.

## PostgreSQL at high volume

Inspect attendance, audit, outbox/event history and job tables for growth. Propose time partitioning only with tested school/date query patterns, retention, partition creation and PostgreSQL uniqueness constraints accounted for. Partitioning does not automatically distribute writes across machines. Keep short-lived delivery history separate from permanent school records; archival cannot silently remove required attendance or finance history.

Use bounded database pools per process, with a total connection budget across all nodes; evaluate self-hosted PgBouncer when needed and verify transaction-local RLS context. Batch imports and inserts within safe transaction sizes. Avoid excessive indexes and large OFFSET scans. Benchmark RLS under the real runtime role. Guard expensive exports/report generation with quotas.

Read replicas are suitable only for explicitly stale-tolerant views. Permissions, revocation, payment confirmation, academic approvals and read-after-write flows use the authoritative primary or a tested consistency mechanism. Replicas are not a replacement for backups.

## Multi-instance realtime and storage

Every instance must receive or discover the committed events needed by its own connected clients. A single competing-consumer queue is not sufficient for broadcast to subscribers on other nodes. Use per-instance durable cursors or a self-hosted fanout component when measurements justify it. PostgreSQL notification is a wake-up hint only; durable replay/resync remains authoritative. Bound instance lifetimes/cursors and retire stale consumers.

Share session state, file access and compatible application/config versions across nodes. Review Next.js cache/build coordination and server-action encryption configuration for the pinned version. Private responses remain uncached by default. No sticky-session correctness assumption. SSE and polling require load tests with real proxy behaviour and revocation.

## Shard-readiness without premature sharding

Use globally unique IDs and school-scoped keys; never create business relationships across schools' record sets. Keep tenant resolution behind a server-side context that can later include validated database placement. A global account/membership directory needs an explicit consistency model before schools move to separate databases; don't duplicate membership/permissions without a revocation strategy.

Future tenant migration: freeze or capture writes → copy school-scoped rows/files and history → verify constraints/counts/hashes/financial totals → catch up changes → atomically switch placement → reconnect jobs/live clients → monitor → retain recovery path. Reconcile provider callbacks and queued jobs against current placement. This is a future migration design, not implemented routing or a promise of effortless sharding.

## Capacity verification gate

Step 01 establishes measurable tooling; step 07 measures realtime/worker recovery; step 10 benchmarks batch rendering; step 11 tests concurrent finance writes; step 13 runs production-like load and recovery tests. Add a benchmark profile for the million-student target before advertising that scale; larger synthetic datasets may require dedicated infrastructure and are not claimed as run in the initial VPS pilot.

Suggested initial budgets to validate with product needs: p95 interactive reads under 500 ms server time, ordinary mutations under 1 s excluding network/provider work, connected-client update lag under 5 s, and sustained target peak with at least 30% resource headroom. These are proposed engineering targets, not an SLA. Heavy reports are asynchronous and use separately agreed throughput/deadline budgets. Record actual dataset, query mix, hardware, tool/commit, p50/p95/p99, errors, tenant fairness, recovery and bottlenecks; revise budgets explicitly if evidence or school needs require it.

No success criterion is “we have an index” or “PostgreSQL supports millions.” Pass/fail depends on measured workload and recovery, including the largest tenant, academic publication bursts, start-of-day attendance and mass report generation. Document failed capacity tests and the next infrastructure change.
