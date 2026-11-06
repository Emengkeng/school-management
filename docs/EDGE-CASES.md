# Edge-case and failure register

This is a living register, not a claim that every possible case is known. Each row is a required behaviour and verification scenario. Link implementation tests/evidence here as steps complete; all are currently planned. Add discoveries under stable identifiers. “Reject” means a safe error without revealing another school's data.

## Isolation, identity and domains

| ID | Scenario | Handling and required verification |
|---|---|---|
| T01 | Guess another school's record ID | Deny queries and mutations; test API, actions, files, export and SSE |
| T02 | Reused DB connection retains tenant | Transaction-local context; alternating-tenant and rollback tests |
| T03 | Missing tenant, direct DB path or worker | Default deny; exercise every repository/job entry path |
| T04 | Cross-school foreign key | Constraint rejects even if service validation fails |
| T05 | Role in A accidentally applies in B | Recompute membership per school; test mixed-role account |
| T06 | Guardian link/staff role revoked mid-session | Deny next request and terminate/revalidate live access; cached URLs fail |
| T07 | Invalid/spoofed forwarded host | Trust configured proxy only, normalise/allowlist; no tenant fallback |
| T08 | Domain stolen/reassigned/DNS removed | Ownership verification, stale mapping removal, audit and fallback address |
| T09 | TLS validation/renewal/rate limit failure | Domain remains inactive or alerts; platform subdomain works |
| T10 | Login callback/open redirect/domain mismatch | Approved destinations, one-time exchange, CSRF checks; no token leaks |
| T11 | Shared phone or school switching | No inherited drafts/cache; clear scoped data and show active school |
| T12 | Expired/replayed invite/reset; lost phone | Hashed single-use tokens, throttling and reviewed recovery; no automatic guardian relinking |
| T13 | Support operator accesses private data | Narrow expiring access with reason/audit; no default broad dashboard |
| T14 | Cached search/chart/export crosses tenant | Scoped generation/storage/cache keys and negative tests |
| T15 | RLS bypass via owner/view/function | Separate runtime role, review functions/views, test actual DB role |

## Realtime and offline

| ID | Scenario | Handling and required verification |
|---|---|---|
| R01 | Disconnect/missed/out-of-order events | Cursor replay/current-record fetch; old version cannot overwrite newer |
| R02 | Replay cursor expired or forged | Authorise replay; full resync; no event enumeration leak |
| R03 | Worker crashes before/after delivery | Durable outbox and effect keys; retries may duplicate delivery but not business effect |
| R04 | Notification/push/email unavailable | Inbox remains; bounded retry/dead letter and visibility |
| R05 | Two simultaneous edits | Optimistic conflict or serialisation; preserve both intents for review |
| R06 | Offline event after schedule/role change | Revalidate and show conflict; never silently force acceptance |
| R07 | Wrong device clock/timezone | Store reported/received times, UTC and school dates; server deadline policy |
| R08 | Logout/session expiry with pending work | Pause sync; revalidate same identity; do not upload under another account |
| R09 | Excess live connections/proxy buffering | Limits, heartbeat, expiry, fallback polling and VPS test |
| R10 | High event volume or school abuses resources | School-scoped quotas and bounded replay/queues; no global starvation |

## School day and learning

| ID | Scenario | Handling and required verification |
|---|---|---|
| L01 | No lesson update | Show unconfirmed, remind/escalate; not automatically absent/ended |
| L02 | Teacher forgets attendance | Keep unrecorded separate from present/absent; completeness report |
| L03 | Double start/end or end before start | Idempotency and valid transitions; reviewed retrospective correction |
| L04 | Holiday/substitute/room clash | Calendar exceptions and scoped substitute permissions; conflict checks |
| L05 | Midyear transfer/repeat/subject combination | Effective-dated enrolment; no rewritten history or unrelated subject access |
| L06 | Late arrival, early departure, excused absence | Separate statuses/revisions and school-approved alerts |
| L07 | Guardian disputes/corrects attendance | Reviewed explanation and correction audit; alert amendment if needed |
| L08 | Malware/fake type/path traversal/oversized file | Quarantine, validate, scan, limit, safe generated path and private serving |
| L09 | Upload succeeds but DB fails or vice versa | Staged metadata, finalisation, expiry cleanup and orphan reconciliation |
| L10 | Quota exhausted during concurrent uploads | Reserve/check quota transactionally; release failed reservations |
| L11 | Material replaced/deleted after download | Version/tombstone; acknowledge downloaded copies cannot be recalled |
| L12 | Forum abuse, XSS, spam, bullying | Sanitisation, moderation, rate limits, reporting and escalation |
| L13 | Late/duplicate/resubmitted assignment | Stable submission IDs, versions, extension and explicit server receipt policy |
| L14 | Deadline changed after submission | Version instructions and notify; don't erase valid earlier work |
| L15 | Non-digital assignment or no student phone | Physical submission recording and school-supported access process |
| L16 | Parent chat exposes other children | Participant/child scope, confidential attachments and negative access tests |
| L17 | Staff leaves conversation or report unresolved | Revoke access, retain under policy and authorised reassignment |
| L18 | Withdrawn enrolment accesses old materials | Apply documented historical-access policy, not indefinite membership |

## Marks and reports

| ID | Scenario | Handling and required verification |
|---|---|---|
| A01 | Missing mark versus zero/absence/exempt | Explicit states and configured calculation policy; never coerce silently |
| A02 | Invalid maximum, weights or coefficients | Validate version before entry/publication; numeric boundary tests |
| A03 | Rounding/ties/ranking discrepancy | Exact decimal arithmetic and fixed policy; independently computed examples |
| A04 | Rule changes after marks/publication | Version/snapshot; approved revision, not silent recalculation |
| A05 | Concurrent entry/review/publication | Version checks, approval tied to input version, atomic publication state |
| A06 | Missing marks/cohort partially ready | Block or explicitly authorised partial publication with clear status |
| A07 | PDF generation failure/long names/multiple pages | Retry artifact job; no ready link until complete; visual print QA |
| A08 | Student name/class changed later | Historical snapshot unchanged; corrected edition explicitly linked |
| A09 | Draft report accessed by parent | Deny unpublished documents and alternate download paths |
| A10 | Teacher edits after submission/approval | Controlled reopen; approvals invalidated; full revision trail |

## Report templates and batch generation

| ID | Scenario | Handling and required verification |
|---|---|---|
| A11 | Supplied PDF/image or unsupported document | Use as design reference; prepare approved DOCX, no automatic fidelity promise |
| A12 | Broken/unknown placeholders, subject loop or required field | Reject activation/generation with safe actionable errors; no silently blank marks |
| A13 | Template/font/renderer changes mid-batch | Pin versions/inputs; new batch/revision required; preserve old artifacts |
| A14 | Conversion timeout, malformed or hostile document | Isolated restricted converter, bounded resources, validate output, quarantine failures |
| A15 | Batch partially fails, repeats or is cancelled | Per-student status/retry, stable IDs, fixed roster; explicit partial export manifest |
| A16 | Duplicate names/oversized class archive | Stable filenames/IDs, safe paths, bounded streamed or background archives |
| A17 | Parent accesses class ZIP or editable export | Deny; only their child's published permitted artifact; audit staff exports |
| A18 | Edited DOCX differs from stored academic record | External edits do not sync; approved snapshot/revision remains authoritative |

## Fees and payments

| ID | Scenario | Handling and required verification |
|---|---|---|
| F01 | Repeated invoice generation/payment submit | Tenant-scoped idempotency; one financial effect |
| F02 | Same idempotency key, different body | Reject conflict; never reuse previous result for changed amount |
| F03 | Partial/overpayment or multiple children | Explicit allocations and unallocated credit; no automatic cross-child transfer |
| F04 | Simultaneous allocations/refunds | Serialise/lock, enforce available amount and currency |
| F05 | Forged/replayed/duplicate webhook | Verify provider authenticity, event identity and payload match |
| F06 | Lost response, delayed/reordered callback | Pending/ambiguous state and provider query; no blind repeat charge |
| F07 | Late confirmation after expiry or module disable | Reconcile trusted confirmation; retain operational callback handling |
| F08 | Amount/currency/school/provider-account mismatch | Quarantine/review; do not issue confirmed receipt |
| F09 | Refund/reversal/chargeback after allocation | Linked compensating records; reflect invoice balance; keep original receipt history |
| F10 | Manual cash payment recorded incorrectly | Approval and reversal/correction; no destructive posted edits |
| F11 | Discount/scholarship/fee change midyear | Effective adjustment with reason; historical invoices preserved |
| F12 | Settlement less than gross due to provider fees | Separate fees/settlement from student principal; reconciliation report |
| F13 | School subscription confused with student fees | Separate ledgers and provider routing; test identifiers and access |
| F14 | Unsupported decimal XAF/negative amounts | Validate minor units and permitted adjustment type; reject malformed inputs |
| F15 | Duplicate receipt number or provider reference | Scoped unique constraints and retry/review; no gapless promise |
| F16 | Student departure while fees outstanding | Preserve authorised balance/history; school policy decides collection |

## Operations, modules and lifecycle

| ID | Scenario | Handling and required verification |
|---|---|---|
| O01 | Disable/downgrade with active jobs/data | Dependency preview, read-only retention, complete existing obligations |
| O02 | Feature hidden in UI but API still works | Enforce all server entry points and new worker actions |
| O03 | Import duplicate/partial/wrong-school rows | Preview validation, stable IDs and reviewed atomic/batched recovery |
| O04 | Year rollover run twice/wrong promotion | Idempotency and preview; historical records preserved |
| O05 | Disk full/DB unavailable/worker stopped | Safe failure, health alerts, bounded uploads and recovery queue |
| O06 | Backup exists but cannot restore | Scheduled restore drill covering DB, files, secrets and permissions |
| O07 | Restore one school from shared backup | Restore to isolated environment; authorised scoped extraction/reconciliation |
| O08 | Schema migration fails/old app still runs | Compatible staged migration; verified recovery, no automatic destructive rollback |
| O09 | School export/deletion/retention request | Authorised scoped export; policy-driven purge and backup expiry handling |
| O10 | Compromised account/secret or device | Revoke sessions, rotate credentials, assess scope, audit incident |
| O11 | Third-party outage or vendor replacement | Adapter boundary; manual/in-app fallback and retained durable work |
| O12 | Inherited tutorial assets lack licence | Verify rights before distribution or replace affected assets |
| O13 | Staff error or malicious privileged edit | Least privilege, review separation, append-only financial/academic revisions |
| O14 | Expired export URL or CSV formula injection | Expiring authorised downloads and spreadsheet-safe export values |
| O15 | Suspension locks out essential records | Defined school read-only/export grace policy; no automatic destruction |

## Scale and workload

| ID | Scenario | Handling and required verification |
|---|---|---|
| S01 | Million records confused with concurrent users | Benchmark explicit workload/hardware; no unsupported capacity statement |
| S02 | Large school query or DB pools overwhelm primary | Cursor pagination, scoped indexes, bounded pools/timeouts; skew/load tests |
| S03 | Large tenant/report batch starves school-day updates | Separate heavy queues, fair per-school concurrency and backpressure tests |
| S04 | Billions of attendance/history rows | Measured volume, retention/partition/archive plan; query/restore benchmark |
| S05 | Multiple app nodes miss events or use local files | Shared private storage and per-instance replay/fanout; cross-node reconnect test |
| S06 | Replica lag exposes stale permissions or financial state | Authoritative primary for critical checks/read-after-write; lag tests |
| S07 | Tenant migrates database while jobs/callbacks run | Validated placement/cutover, reconciliation and recovery path; no duplicate effects |
| S08 | Growth exceeds backup/restore/failover budget | Measure full recovery at target size; change infrastructure before promising SLA |

## Scoped roles and delegation

| ID | Scenario | Handling and required verification |
|---|---|---|
| P01 | School admin grants platform powers | Permission catalogue/delegation boundary rejects; direct endpoint test |
| P02 | User edits held role to self-escalate | Check delegable authority/conflicts; no self-granted privileged actions |
| P03 | Class head or teacher acts outside scope | Class/subject/campus relationship checks; multiple-role negative tests |
| P04 | Role expires/revoked with cached session/live connection | Invalidate/revalidate and deny future work; offline replay rechecks authority |
| P05 | Last owner demoted/deleted | Require authorised ownership handover/recovery, transaction/concurrency protection |
| P06 | Submitter approves own refund/report under dual-control policy | Reject self-approval; verify all mutation paths |
| P07 | Custom role removes/renames a permission used by UI | Server denies unknown/missing capability; version definition and audit changes |
| P08 | Support/auditor role implies broad record access | Explicit restricted content/export grants only; expiry and audit test |

## Permanent matricules, linking and transfers

| ID | Scenario | Handling and required verification |
|---|---|---|
| I01 | Matricule collision/concurrent issuance or registration retry | Global unique constraint and bounded retry/idempotency; never school-scoped uniqueness |
| I02 | Same human registered twice with different IDs | Restricted evidence-based review; unique number is not proof of one human record |
| I03 | Typo/case/format variants or unsupported national ID | Canonical normalisation/check character, authority namespace and verified status; no silent new identity |
| I04 | ID guessed/shared or supplied for wrong child | No ID-only access/recovery/linking; exact lookup privacy, school-bound claim and authority verification |
| I05 | Two competing transfers or repeat completion | Versioned approval/atomic finalisation, idempotency and reviewed enrolment overlap |
| I06 | Source school unavailable/refuses/left platform | Reviewed identity-link appeal; historical release may remain unavailable; no fabricated permission |
| I07 | Cancelled/expired/failed transfer | No silent withdrawal/activation; clean pending grants and notification states |
| I08 | Transfer grant expires/revoked after export | Deny future access; exported copies follow documented retention, no remote-recall claim |
| I09 | Wrong duplicate merge/split or identity dispute | Privileged review, preserve aliases/provenance and controlled compensating correction; no automatic guardian expansion |
| I10 | Report lacks platform matricule or displays stale/wrong one | Required snapshot field/template validation; corrections create linked edition, no silent historical rewrite |
| I11 | Transfer copies fees/private messages/guardians by default | Exclude from default manifest; explicit separate lawful authority/reverification; source records remain isolated |
| I12 | National identifier conflict/import claim or future shards | Unverified claims cannot reserve verified binding; central uniqueness/placement and evidence review; never reuse IDs |

## Adding a case

Record ID, trigger, expected user-visible state, invariant, recovery owner and automated/manual evidence. Link the affected roadmap step. If an important policy is unresolved, disable the dependent action rather than inventing an irreversible default. No feature is complete solely because its happy path works.
