# Verification strategy

## Checks by risk

- Pure domain tests: attendance transitions, capability evaluation, report calculations and financial allocations using independently specified expected results.
- PostgreSQL integration: real constraints, restricted-role RLS, pooled connection reuse, transactions, concurrent edits, idempotency, outbox/lease recovery and migrations.
- HTTP/action integration: authentication, permissions, CSRF/origin, tenant resolution, malformed input and non-public error responses.
- Browser journeys: administrator → teacher → student/guardian across two synthetic schools; responsive mobile views, keyboard access, English/French labels and empty/error/offline states.
- Realtime/failure tests: reconnect/replay, revoked access, dead worker, lost provider response, scan failure, disk/storage errors and duplicate requests.
- Visual verification: report cards and receipts printed/rendered with long names, accented text, maximum subjects and multiple pages; no clipped content.
- Operational evidence: reproducible clean setup, deploy smoke check, restore drill and disabled external side effects during recovery.

Use unit/integration and browser tools appropriate to the chosen versions (planned Vitest and Playwright). Do not write tests that merely repeat implementation details. Exact commands are added when the toolchain exists.

## Template and capacity evidence

Step 10 must verify externally designed templates, placeholder/loop errors, pinned fonts/conversion fidelity, bulk progress/retry, fixed batch inputs and parent exclusion from class archives. See REPORT-CARDS.

Capacity tests follow SCALE: distinguish total students from concurrency, measure realistic attendance volume and skew, bound DB pools, prove per-school job fairness, and test report bursts separately from interactive traffic. Record hardware/dataset/tool/revision and p95/p99/errors. A single-VPS smoke test cannot substantiate millions-of-students capacity. Required scale cases are S01–S08.

## Required synthetic fixtures

School A and B; user teacher in A/guardian in B; unrelated guardians; revoked guardian; departed teacher; authorised substitute; student with subject combination; student transfer; partially paid invoice; overpayment; missing versus zero mark; disabled module; verified and unverified hostname. Include a third unauthenticated actor and a restricted runtime database role.

## Critical invariants

1. Every path rejects another school's data: queries, mutations, search, files, exports, caches, jobs, SSE and notification recipients.
2. No success acknowledgement before a durable save; no lesson event implies pupil presence.
3. Duplicate requests/events cannot duplicate charges, allocations, reports or other business effects.
4. Published reports and posted money retain revision history.
5. Disabled capabilities block new server-side work while preserving existing obligations and authorised history.
6. Unpublished/quarantined artifacts cannot be downloaded.
7. Lost events/connections recover without silently losing records.
8. Backups can actually be restored; logs and fixtures contain no real student secrets.

## Per-step completion record

Record commands, outcome, commit/revision and relevant edge-case IDs in STATUS. If browser/provider/VPS testing is unavailable, state the exact unverified part and keep dependent release gates blocked. Do not run irrelevant broad tests repeatedly for documentation changes.

## Pilot readiness gate

An authorised school has reviewed guardian verification, moderation/oversight, grading and attendance rules, retention/privacy and financial responsibilities. Isolation tests pass; teacher/parent journeys work on representative devices; required recovery/monitoring is verified; support owner exists. Online payments remain off until a real provider sandbox and settlement/reconciliation tests pass. Legal requirements need qualified local verification, not a claim inferred from these documents.
