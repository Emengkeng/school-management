# Product and scope

## Objective

Give schools one trustworthy operational record and give teachers, students and verified guardians timely access to the parts relevant to them. Support many independently administered schools with English/French interfaces, low-bandwidth use and self-hosted infrastructure.

## Roles

| Role | Scope |
|---|---|
| Platform operator | Tenant onboarding, platform health, subscriptions and domains; school content access is exceptional and audited |
| School administrator | School setup, users, permissions, enrolment, schedules, notices and oversight |
| Academic supervisor | Assigned academic review, missing registers, substitutions, results approval |
| Bursar/finance officer | Charges, receipts, allocations, reconciliation; refunds require explicit permission |
| Teacher | Assigned lessons, attendance, materials, assignments, marks and permitted conversations |
| Student | Enrolled subjects, own assignments/feedback and permitted discussion |
| Guardian | Authorised children's timetable, attendance, published results, fees and conversations |

Roles are permission bundles, not universal visibility. Teachers do not automatically read all parent contacts or finance records. Separate permission to enter marks, approve results, publish reports, record payments and approve refunds.

## Modules

Foundation (always): tenant identity, memberships, permissions, academic years, enrolment, guardian links, audit, safe storage and exports. Optional: lessons/timetable, attendance, notices, parent portal, resources/forums, assignments, messaging, assessments, report cards, fee records, online collection, custom domains and multiple campuses.

Deferred extensions: admissions portal, transport, boarding, library, inventory and payroll. Do not imply these are included in the first release. No biometric tracking, continuous location, public student profiles or automatic AI grading in the baseline.

## Experience

- Teacher mobile home: today's lessons, pending attendance and unresolved sync actions.
- Administrator: actionable exceptions rather than only charts; substitutions, unrecorded lessons, account invitations and approvals.
- Guardian: child selector, dated timeline, timetable, important alerts and acknowledgement requests.
- Student: subjects, material library, deadlines and discussion threads.
- Finance: outstanding balances, unverified transactions, reconciliation and receipt history.

Parent visibility is not notification spam. Lesson events update timelines; absence alerts and urgent changes have school-approved notification rules. Guardians may acknowledge/explain; explanations require review before changing school records.

## School-designed reports and scale

Report cards use externally prepared, school-approved templates, initially DOCX, filled from approved marks and exported as individual/bulk DOCX and print PDFs. No in-browser report designer is required. See REPORT-CARDS.

Millions of student records is the long-term design goal; concurrent usage and event volume must be measured separately. A single VPS is a pilot topology, with staged growth in SCALE. Do not claim demonstrated million-student capacity before benchmark evidence.

## Pilot and success

Pilot one consenting school with at least two classes and a second synthetic tenant for isolation tests. Agree responsibility for lesson updates, moderation, absence escalation and account correction. Measure register completeness, teacher time, guardian activation, sync delay, unresolved corrections and payment reconciliation accuracy. Do not claim nationwide school practices without validating them.

## Release boundaries

Foundation → live school day → learning/communication → academics → finance → custom domains/production operations. All tenant/security foundations are required from the first functional release. Each optional module can be piloted independently after its dependencies pass.
