# Role and permission management

## Model

Use role-based permission bundles with relationship and scope checks, not a single hardcoded role column. A global user may have multiple school memberships. Each membership may hold multiple effective-dated role assignments; a role assignment has school/campus/class/subject scope where applicable. Global platform roles and school roles are separate namespaces.

School administrators can create/edit school-specific roles from a platform-defined permission catalogue. A custom role cannot create a new privileged action, grant platform powers, bypass tenant isolation or exceed the assigner's delegable authority. Built-in roles are safe starting bundles, not permission to read every record. Feature entitlements remain separate.

## Default role bundles

| Role | Intended scope | Key exclusions unless explicitly delegated |
|---|---|---|
| Platform operator | Tenant provisioning, platform subscriptions, domains, health | No default school-content access, no automatic student-identity merge |
| School owner/admin | School setup, memberships, academic structure and delegation | Cannot become platform operator or override another school |
| Registrar/admissions officer | Student intake, identity linking, enrolment and transfer requests | Cannot browse platform-wide student records or alter marks/posted fees |
| Principal/academic supervisor | Academic review, scoped oversight, publication when granted | Refunds/identity merges not implied |
| Class head | Assigned class register, coordination, comments, approved cross-subject mark entry | No default all-school marks entry or report publication |
| Subject teacher | Assigned groups/subjects, lessons, attendance, materials and marks | Other subjects/classes, unrelated guardian/financial data |
| Bursar/finance officer | Scoped invoices, verification, receipts and reconciliation | Refund approval and role management separate |
| Student | Own enrolled resources, submissions and published records | Other students' individual marks/fees or teacher drafts |
| Guardian | Authorised children's permitted records/conversations | Automatic access to all children, class archives or previous schools |
| Identity review officer | Explicit limited global duplicate/link/appeal review | Exceptional audited function, not ordinary platform support |

Default distinct grantable permissions include school.settings.manage, memberships.invite, roles.manage, roles.assign, students.register, students.link_existing, enrolments.manage, transfers.request, transfers.release, transfers.accept, transfers.review_appeal, attendance.record, attendance.correct_closed, marks.enter, marks.review, reports.template_manage, reports.generate, reports.publish, finance.record, finance.verify, finance.refund_request, finance.refund_approve and audit.view. These are planning names; implementation must version and document exact keys and their scopes.

## Decision procedure

1. Authenticate an active session and resolve the verified school.
2. Validate active membership and effective role assignments at request time.
3. Check school/module state and entitlement for the action.
4. Evaluate the catalogue permission and assignment scope, including target school and subject/class/campus.
5. Check relationships (teacher assignment, guardian link, student ownership or transfer grant) and record state/version.
6. Enforce separation-of-duty policy and deny on unknown/invalid context.
7. Record privileged changes and execute the domain transaction under tenant isolation.

In-scope grants combine, but a grant in one school never extends another. Relationship limits, suspension, disabled capabilities and review requirements cannot be overridden by accumulating roles. If explicit deny overrides are introduced later, specify/test their precedence before use; do not invent inconsistent deny semantics in separate screens.

## Management workflow

1. Admin selects a built-in or custom role and inspects its permissions/dependencies.
2. Changes show who gains/loses access and which scopes apply; finance, publishing, identity and role-administration privileges are clearly marked.
3. Validate delegable authority and conflicting roles. Sensitive assignments require the school's approval policy, with a documented initial-owner bootstrap exception.
4. Save versioned role definition/assignment with actor, reason and effective period; invalidate permission caches and revalidate active sessions/live subscriptions.
5. Expire temporary substitute/acting-class-head authority automatically; reassignment does not transfer passwords or sessions.
6. Revocation stops future actions and queued unauthorised new work, while preserving history and committed obligations.
7. Review access periodically and on staff departure. Never remove/demote the last active owner without an authorised ownership transfer or documented recovery.

## Separation and recovery

Do not let users self-escalate by editing a role they hold. Protect roles.manage and roles.assign independently and restrict delegation of those permissions. Where dual approval is configured, creator/requester cannot approve their own results/refund/transfer release. Very small schools may explicitly choose a documented single-authority operational policy, with high-impact actions still fully audited; do not silently bypass a dual-control setting.

No shared staff accounts. Emergency support grants are reasoned, scoped, expiring and reviewed. Account recovery does not automatically confer ownership or approve guardian/identity links. Read-only auditors do not gain message-body or finance-export access simply through audit.view.

## Verification

Test horizontal scope and vertical privilege escalation, direct API/Server Action calls, role-definition edits, self-grants, multi-role accounts, class-head versus subject-teacher entry, last-owner protection, expiry/revocation during SSE/offline work, denied report/archive access and finance approval separation. See P01–P08 in EDGE-CASES. UI may hide actions for usability; server services and restricted DB access enforce them.
