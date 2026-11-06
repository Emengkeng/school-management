# Ordered business workflows

Each mutation follows architecture transaction/audit/outbox rules. All steps are tenant-scoped and feature/permission checked. “Notify” means save an in-app record and queue configured delivery, not assume the recipient read it.

## W01 School onboarding
1. Platform operator creates school, unique slug and initial owner invitation.
2. Owner verifies account; invitation is single-use, expiring and school-bound.
3. Configure language, timezone, campuses, academic year/terms and modules.
4. Create/import subjects, classes, staff and student records with validation preview.
5. Establish enrolments, teaching assignments and verified guardian links.
6. Publish timetable; activate the pilot after the readiness checks pass.
7. Optionally configure a verified custom domain after its module is available.
Failure: incomplete imports remain staged; do not publish half-valid relationships. Retried imports use stable external IDs and a reviewed duplicate policy.

## W01b Global student identity and transfers

1. Registrar checks existing platform matricule/verified evidence through protected identity resolution, without a public student directory.
2. Issue a permanent globally unique matricule only for a verified new identity; uncertain matches remain provisional for review.
3. Existing identity uses a school-bound claim/transfer request; knowing the ID does not authorise linking or history access.
4. Verify student/guardian authority, source release manifest and destination acceptance separately; handle unavailable source through reviewed appeal.
5. Complete approved enrolment transition idempotently, retaining the same platform matricule and source-owned history; only approved snapshots are shared.
6. Reverify destination guardian relationships, preserve report identity and audit aliases/corrections.

Follow STUDENT-IDENTITY for states, permissions, national/local numbers, concurrent requests and duplicate correction. Read ROLE-MANAGEMENT before assigning registrar/reviewer permissions.

## W02 Daily lesson and attendance
1. Generate dated lesson occurrences from effective timetable and calendar exceptions, idempotently.
2. Teacher views only assigned occurrences, including authorised substitutions.
3. Start records reported time and server receipt time. Parent timeline says “teacher reported started.”
4. Teacher explicitly completes attendance; initial statuses are unrecorded, not present.
5. Late arrival/early departure updates create revisions. An absence alert follows school review/grace rules.
6. End records actual/reported time, topic and optional homework; missing end remains unresolved.
7. Supervisor reviews unresolved sessions and corrections; changes retain reasons/history.
Failure: an offline event remains pending; a stale assignment or cancelled occurrence triggers conflict review. Never auto-end a lesson as if confirmed. Guardian explanation is submitted → reviewed → accepted/rejected; acceptance does not erase the original observation.

## W03 Timetable change
1. Authorised staff select one occurrence or a future recurring change.
2. Check teacher, room/group and student conflicts; distinguish warnings from hard constraints.
3. Save a new version/exception with effective date and reason.
4. Preserve completed occurrences; update only eligible future occurrences.
5. Notify affected users; show changes in the timeline. A substitute receives scoped permissions for the relevant interval.
Failure: do not silently apply a new schedule over recorded attendance or an in-progress lesson.

## W04 Materials and subject forums
1. Teacher chooses an assigned subject space and audience.
2. Upload enters quarantine; check permission, quota, size, type, scan and storage result.
3. Publish only approved material; show title, topic, version and audience.
4. Students access according to dated enrolment/access policy; guardian summaries are separate.
5. Discussion supports threads, moderation, reports, locking and safe formatting.
6. Corrections publish a new material version; removals show an appropriate tombstone.
Failure: storage/scan failures never produce downloadable approved records. Malware is not retried as ordinary publishing. Previously downloaded files cannot be remotely recalled. Cross-school reuse requires explicit authorised copying and licensing review.

## W05 Assignments
1. Teacher drafts instructions, audience, due time, accepted formats and physical/digital mode.
2. Publish an immutable instruction version; changes are recorded and notified.
3. Student saves a draft, uploads files and submits; submission acknowledgement requires server acceptance.
4. Retried submissions are deduplicated. Resubmissions preserve versions.
5. Teacher reviews and drafts feedback; release explicitly.
6. Guardian sees permitted status and released feedback for their child.
Failure: client timestamp alone does not prove timely submission. Define offline/late accommodation before use; store receipt and reported times. Teacher extensions can be student-specific and audited. Disabling digital submission must preserve existing work.

## W06 Announcements and parent conversations
1. Sender selects class/subject or verified child/guardian recipients.
2. Server computes eligible audience and stores the intended recipient snapshot/access policy.
3. Publish notice or create private school-managed conversation.
4. Queue deliveries and show inbox; optional acknowledgement is distinct from delivery/read state.
5. Guardian replies/explains, teacher responds within school expectations.
6. Escalation routes to authorised school staff; moderation/access is disclosed.
Failure: prevent unrelated guardians seeing one another's child-specific information. Honour revoked memberships/links even for old URLs and live sessions. Emergency notices must explain fallback contact procedures; this is not an emergency dispatch service.

## W07 Marks and report cards
1. Configure versioned scheme: assessments, weights, coefficients, maxima, rounding, missing/absent/exempt rules, pass rules and optional ranking/ties.
2. Validate scheme totals and eligible subjects; lock a version before entry.
3. Teachers enter scoped draft marks; distinguish zero from missing and absent.
4. Validate range, duplicates and completeness; submit for review.
5. Reviewer returns with reasons or approves. Prevent approval from mutating mark inputs unnoticed.
6. Select the school-approved external template version, validate required fields, and freeze approved result snapshots. An authorised class head may enter marks across assigned subjects only with explicit permission.
7. Staff request an idempotent class/cohort batch; bounded workers fill templates and generate individual DOCX/PDF, a ZIP and optionally a merged print PDF. Show progress and failed students; export readiness is separate from publication.
8. Publish reviewed artifacts to permitted students/guardians; draft URLs and class archives remain inaccessible to parents.
9. A correction reopens a controlled revision, recalculates with an explicit policy version, reapproves and republishes a linked snapshot.
Failure: failed PDF generation does not mark a report ready. No partial cohort publication without explicit policy. A student changing class/name later does not rewrite old reports. Verify long names, French accents, multi-page reports and print layout visually. School supplies/designs its template externally; follow REPORT-CARDS for onboarding, approval, bulk failure handling and format limits.

## W08 Fees and manual payments
1. Finance sets effective fee schedule and individual scholarships/waivers with approval reasons.
2. Generate student invoices idempotently; snapshot charge description, currency and period.
3. Record cash/transfer reference and evidence; apply verification/approval policy.
4. Post verified payment and allocations transactionally; issue a uniquely numbered receipt.
5. Parent sees charges, confirmed payments, unallocated credit and remaining balance separately.
6. Reconcile against cash/bank records; resolve discrepancies without editing posted history.
7. Approved refunds/reversals create linked compensating records and revised balance.
Failure: duplicate references are reviewed within provider/school scope; names alone never identify a payment. Do not silently use one child's credit for another or one school's money for another school.

## W09 Optional online collection
1. Complete provider, settlement, fee, refund and payer policy decisions; do not enable live collection before these are resolved.
2. Server creates attempt for explicit invoice allocation and amount; stable operation key.
3. Send provider request outside DB transaction; persist request/result safely.
4. Redirect return page shows pending until trusted confirmation. Never trust client success.
5. Verify webhook authenticity/replay and match provider account, school, currency, amount and external ID.
6. Deduplicate provider event; confirm transaction and allocations once; queue receipt.
7. Query provider/reconcile when response is lost, webhook delayed, events reordered or amount mismatches.
8. Track settlement separately from payment confirmation and handle refunds through confirmed workflows.
Failure: quarantine unmatched events for finance review. Never retry an ambiguous charge blindly. Record provider fees separately from student principal; determine who bears fees before launch. No custodial wallet or pooled school balances in baseline.

## W10 Year rollover, departure and module changes
1. Close academic/financial periods with unresolved-item report.
2. Archive history, create next year and preview proposed promotions/repeats/transfers.
3. Apply reviewed enrolments; do not copy grades, guardian consent or permissions blindly.
4. Revoke departed staff/student access as policy requires; preserve records and reassign duties.
5. Module disable/downgrade previews dependencies, active work and retention impacts; apply FEATURES lifecycle.
6. School departure triggers authorised export and documented retention/deletion, not immediate destruction.
