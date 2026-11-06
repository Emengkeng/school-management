# Data model contract

Logical design, not a generated migration. Use UUID identifiers, UTC timestamps, school-local dates/timezone where calendar semantics require them, optimistic versions for mutable shared records and explicit lifecycle states. Default school timezone: Africa/Douala, configurable. No real student fixtures.

| Area | Principal entities and invariants |
|---|---|
| Platform | users, sessions, schools, school_domains, memberships, role_definitions, role_permissions, role_assignments, permission_grants, operator_access_grants; hostnames globally unique; school membership unique per user/school |
| Configuration | academic_years, terms, campuses, levels, classes, subjects, grading_policy_versions; avoid overlapping active periods unless explicitly supported |
| Global student identity | student_identities, external_student_identifiers, identity_claims, identity_aliases, identity_reviews; globally unique stable platform matricule, restricted identity registry |
| People | school_student_profiles, staff_profiles, guardian_links, enrolments; school-owned profile linked to approved global identity; user login optional; enrolments/links effective-dated |
| Transfers | student_transfers, transfer_approvals, transfer_record_grants, transfer_manifest_items; cross-school orchestration explicitly authorised, historical snapshots minimally shared |
| Teaching | teaching_groups, group_enrolments, teacher_assignments, timetable_versions, timetable_rules, calendar_exceptions, lesson_occurrences; occurrence identity unique per rule/date/version strategy |
| Attendance | attendance_records, attendance_revisions, absence_explanations; one current status per student/attendance session; distinguish school-day and lesson sessions |
| Learning | subject_spaces, materials, material_versions, file_objects, threads, posts, moderation_actions; subject audience tied to enrolment and assignment |
| Assignments | assignments, assignment_versions, submissions, submission_versions, feedback; deadline/version and physical/digital submission explicit |
| Messages | conversations, participants, messages, acknowledgement_requests, acknowledgements; recipients verified server-side; access follows policy and membership |
| Academics | assessment_schemes, assessments, mark_entries, mark_revisions, result_batches, result_approvals, report_snapshots, report_templates, report_template_versions, report_template_assignments, report_generation_batches, report_generation_items, report_artifacts; mark states include missing, exempt, absent and numeric |
| Finance | fee_schedules, invoices, invoice_lines, adjustments, payments, payment_allocations, receipts, refunds, provider_events, reconciliation_runs; immutable posted transactions and compensating entries |
| Modules | feature_definitions, plan_entitlements, school_feature_settings, rollout_rules, feature_changes; effective dates and dependencies |
| Delivery | outbox_events, jobs, notification_inbox, delivery_attempts, processed_operations, audit_events; unique effect keys and audience scope |

## Cross-record constraints

Composite references enforce same-school relationships (for example school/class, school/student and school/assessment). Validate term belongs to year, assessment belongs to subject/group, student was eligible for that dated session, and allocation currency matches invoice/payment. Index common school/date/status and school/user lookups; test real query plans before adding broad indexes.

User account identity and permanent student identity are global; each school student profile, enrolment and school record remains tenant-owned. Platform matricule has a database-wide unique constraint and remains unchanged on transfer. Identity number lookup does not authorise access; use controlled identity linking and approved record grants. Do not merge children solely by names, phone numbers or email. Official national matricule and school admission number are separate namespaces. See STUDENT-IDENTITY for concurrency, collisions, duplicate-person review, aliases and transfer finalisation. Guardian balances are computed only from linked authorised student accounts.

## Money

Store amounts as integer minor units with explicit ISO currency and currency exponent rules; baseline XAF amounts are whole francs. Reject unsupported currencies and fractional XAF inputs rather than silently rounding. Never use binary floating-point for money or result calculations. Use exact numeric arithmetic for marks and defined rounding at explicit stages.

Invoice balances derive from posted charges/adjustments and net allocations, not a manually editable balance. Payment state: initiated → pending → confirmed/failed/expired; settlement and refund states are separate. Late provider confirmation can change a locally expired attempt after verification; expiry alone is not proof that no money moved. Receipts have school-scoped unique numbers; avoid gapless numbering promises. Idempotency scope includes tenant, actor/action and request payload hash; same key with different payload is rejected.

Posted refunds/reversals preserve the original payment and reverse appropriate allocations. Lock or otherwise serialise competing allocations/refunds to prevent over-allocation. Overpayment becomes explicit unallocated credit. School fees are not the platform subscription ledger. Define collection/settlement ownership before any live integration.

## Academic history

Version assessment rules before marks entry. Published reports store immutable snapshots of inputs, formula/version, rounding, comments, student/class labels and approvers. Revisions create a new publication linked to the previous one. Ranking, ties, exemptions and missing marks need a policy; do not invent defaults that silently penalise students.

Template versions are school-owned private documents with content hash, placeholder schema, renderer/font requirements and school approval. Scoped assignments choose a single approved version per class/period. Batch items bind roster, result snapshot and template version; unique identities prevent duplicate generation/publication. Artifact manifests record output formats/hashes and per-student status. External DOCX edits never mutate marks. See REPORT-CARDS.

High-volume attendance/audit/events need measured index, partition, retention and archival plans. Use bounded queries and keep school ownership explicit for future placement. See SCALE; partitioning/sharding is not already implemented.

## Migrations and retention

Migrations are ordered and reviewed, with RLS/constraints included. Backfill tenant identifiers safely before enforcing them; never assign all imported records to a guessed school. Use expand/backfill/contract for disruptive production changes. Document rollback/data repair separately; down migrations do not guarantee recovery of deleted data.

Archive school years instead of deleting history. Retention/deletion requirements may require redaction or controlled purge, including storage and backup expiry. Audit records are access-controlled and retained under policy; “immutable” does not justify retaining personal data forever. Exports are school-scoped, permission-limited, expiring artifacts with an explicit schema/version.
