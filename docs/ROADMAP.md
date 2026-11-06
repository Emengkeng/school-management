# Ordered implementation roadmap

All steps below are planned, not completed. Dependencies are mandatory; numbering is the default execution order. Read relevant WORKFLOWS, FEATURES and EDGE-CASES before each step. Each step includes code, appropriate tests and documentation updates. Do not implement all steps in one unchecked pass.

## 01 — Reproducible foundation

Dependencies: none. Inspect actual branches and asset rights. Establish supported Next.js/React/Node versions, TypeScript checks, formatting/linting, test harness, PostgreSQL local setup, Drizzle migrations and documented environment placeholders. Preserve useful tutorial components selectively; replace hardcoded role/sample records incrementally. Add app/worker entrypoint scaffolding without inventing features.

Acceptance: fresh checkout can install, typecheck, test, build and connect to local PostgreSQL using documented commands; migration works on clean DB; secrets absent; record licence findings. Required cases: O08, O12. Read ARCHITECTURE, OPERATIONS, DECISIONS and SCALE. Establish a reproducible capacity-test profile and metrics baseline without claiming million-student testing has passed.

## 02 — Identity and tenant isolation

Dependencies: 01. Implement maintained local auth, sessions, school/membership registry, permissions, verified platform subdomain resolution, restricted DB roles/RLS and audit. Initial tenant/owner bootstrap must be operator-controlled.

Acceptance: two-school fixtures prove isolation through actual database role and server endpoints; invite/recovery/session revocation work; host spoofing and missing context fail; pooled connection tests pass. Required cases: T01–T07, T10–T15. Read SECURITY-TENANCY and DATA-MODEL. Custom domains remain unavailable.

## 03 — School setup and enrolment

Dependencies: 02. Academic years/terms, classes/subjects/groups, staff assignment, student records, guardian invitations/links, imports and school branding/language. Scope supervisors and finance roles explicitly.

Acceptance: admin configures a school and previews/imports synthetic data with duplicates/errors; guardians see only authorised children; transfers preserve history. Required cases: L05, L18, O03–O04. Workflow W01.

## 04 — Capability controls

Dependencies: 03. Define feature catalogue, local entitlements, dependencies, school settings, rollout flags and read-only/suspension lifecycle. No paid subscription provider required.

Acceptance: server/API/service checks deny disabled modules; different schools have different modules; toggles audited; dependency/disable preview tested. Required cases: O01–O02, O15. Read FEATURES.

## 05 — Timetables and lesson delivery

Dependencies: 04. Recurring rules, dated occurrences, exceptions, substitutions, conflicts, teacher start/end and supervisor exception view.

Acceptance: school calendar generates once; one-day changes preserve history; teacher only acts on assigned occurrences; lesson state is clearly reported. Required cases: L01, L03–L05. Workflows W02–W03. No false attendance claims.

## 06 — Attendance and corrections

Dependencies: 05. Daily/lesson registers, explicit unrecorded status, late/early changes, guardian explanations, review and alert policy.

Acceptance: zero automatic presence; teacher/student/guardian/admin flows tested; disputes preserve history; closed-period edits require permission. Required cases: L02, L06–L07. Workflow W02.

## 07 — Durable delivery and offline school day

Dependencies: 06. Transactional outbox/jobs, worker leases/retries, inbox, scoped SSE replay/polling, reconnect and restricted offline queue. Add delivery adapters only if configured.

Acceptance: worker crash/retry yields one business effect; committed events recover after disconnect; revoked users receive no further protected data; pending offline changes visibly conflict after timetable change. Required cases: R01–R10, T06, T14, S01–S03. Do not claim full offline marks/payments. Read ARCHITECTURE and TESTING.

## 08 — Private resources and moderated forums

Dependencies: 07. Private storage adapter/quarantine/scan, quotas, subject material versions, safe threads/moderation and download policies.

Acceptance: malicious/oversized files rejected; unscanned/other-school files inaccessible; orphan cleanup works; enrolled audience and moderation verified. Required cases: L08–L12, L18. Workflow W04. Document a self-hosted scanning process before enabling uploads.

## 09 — Assignments and communication

Dependencies: 08. Assignment instructions/versions/extensions/submissions/feedback, notices/acknowledgements and school-managed parent conversations. Implement in smaller substeps if needed; record each completed portion.

Acceptance: physical/digital assignments work; retry/resubmit/deadline cases pass; only verified participants access private messages; school oversight policy disclosed. Required cases: L13–L17, R04. Workflows W05–W06.

## 10 — Assessments and report cards

Dependencies: 09. Versioned grading schemes, explicit mark states, submission/review/approval, deterministic calculation, report snapshots, school-approved externally designed DOCX templates, placeholder validation and isolated DOCX-to-PDF conversion jobs. Add per-student batch progress/retry, ZIP/merged-PDF exports and optional staff-only editable DOCX. No browser report designer. Read REPORT-CARDS and SCALE.

Acceptance: independent calculation fixtures match; permissions/revisions/publication gates pass; two school template layouts and printed artifacts visually verified; whole-class batches resume safely, maintain fixed inputs and avoid duplicate publication; parents cannot see drafts or class archives. Required cases: A01–A18, S03–S04. Workflow W07. Resolve school grading rules before pilot publication.

## 11 — Fees and manual payments

Dependencies: 10. Fee schedules, student invoices, discounts/waivers, verified manual payments, allocations, credits, receipts, refunds and reconciliation. Separate platform subscription records.

Acceptance: independently calculated balances, duplicate/concurrent payment tests and controlled corrections pass; parents see only permitted balances. Required cases: F01–F04, F09–F16. Workflow W08. This is school fee management, not a general accounting/payroll system.

## 12 — Optional online payments

Dependencies: 11 and selected provider/settlement policies. Implement adapter, trusted callback verification, ambiguous-state recovery, reconciliation, refunds and module-disable completion behaviour.

Acceptance: provider sandbox proves pending/failed/duplicate/late/reordered/mismatched confirmations; no client-based confirmation; no duplicate charge; finance reviews unmatched events. Required cases: F05–F09, F12–F13, O11. Workflow W09. If provider details are unavailable, leave this step explicitly blocked and module off; do not simulate completion.

## 13 — Domains, VPS hardening and pilot

Dependencies: 11 for core pilot, 12 only if online collection is enabled. Custom domains, TLS lifecycle, production Compose/process configuration, monitoring, backups/recovery, migration/deploy runbooks, capacity checks, accessible responsive UI and school pilot review.

Acceptance: verified custom domain and fallback work; spoofed host fails; restore drill and worker recovery pass; critical isolation/flow tests pass on VPS; production/legal/policy blockers documented. Required cases: T08–T09, O05–O11, O14–O15, S01–S08. Record measured capacity and staged growth recommendations from SCALE; do not advertise an untested million-student deployment. Workflow W10 and OPERATIONS. Production deployment needs a deployment task and actual credentials, not this roadmap alone.

## Scope control

A provider-blocked optional step does not block independent core pilot work in 13. Do not call an optional module complete or enabled while blocked. Remaining deferred modules require new specifications, dependencies, cases and acceptance criteria before implementation.
