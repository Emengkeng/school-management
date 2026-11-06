# Modules, entitlements and feature flags

## Effective capability

Access requires: active school + valid membership/relationship + purchased entitlement (if applicable) + enabled school setting + satisfied dependencies + permission for the action + rollout availability. Deny by default when capability is unknown. A hostname or client-side flag is not sufficient.

| Layer | Owner | Example |
|---|---|---|
| Entitlement | Platform subscription administration | School plan includes online collection |
| School configuration | Authorised school admin | Parent messaging enabled |
| Permission | School role/scope administration | Bursar can post receipts |
| Temporary rollout | Platform release management | New report renderer enabled for pilot school |

Security, tenancy, permanent student identity integrity, permission management, audits and essential recovery cannot be disabled as paid features. Runtime failures must not grant access. Log reasons safely so admins can distinguish missing setup, permission, entitlement or disabled module.

## Initial catalogue

| Key | Dependencies | Behaviour |
|---|---|---|
| timetable | foundation | Dated lessons and recurring rules |
| attendance | foundation; timetable for lesson attendance | Daily attendance may operate independently |
| parent_portal | verified guardian links | Child-scoped views only |
| notices | foundation | Audience-scoped announcements |
| resources | teaching groups, private storage | Material library and versions |
| forums | resources/subject spaces | Moderated discussions |
| assignments | teaching groups, private storage | Physical and/or digital work |
| messaging | verified participants | Private school-managed conversations |
| assessments | academic configuration | Marks and review |
| report_cards | assessments | Approved publication and printable snapshots |
| fees | enrolment/finance permissions | Charges, manual payments and receipts |
| online_collection | fees, configured provider | Provider-backed collection |
| custom_domains | verified DNS/TLS setup | Additional school hostname |
| multi_campus | foundation | Campus-scoped configuration and permissions |

Dependencies are business prerequisites; modules should not require unrelated purchases. Existing valid records must remain recoverable when a capability changes.

## Lifecycle

States: unavailable → available/unconfigured → configured/enabled → suspended or disabled/read-only. Track effective date, actor, reason and history. Before enabling, validate setup; before disabling, show dependants and unresolved jobs/submissions/payments.

Disabling blocks new module actions but preserves records and authorised export/history. Define module-specific read-only policies. If disabling fees/online collection, still accept verified callbacks/reconciliation for already initiated payments and make receipts/refunds operationally available to authorised finance staff. Disabling forums stops new posts; moderation/report handling remains possible. Disabling report cards must not silently revoke access to already issued artifacts without an explicit school policy.

Workers distinguish completing already-committed obligations from creating new actions. Recheck entitlement for new work; don't drop durable financial callbacks or leave uploaded private files orphaned. Module restoration must not replay old notifications or reissue charges.

Entitlements should use an explicit locally stored effective period and grace policy; don't require a remote billing request for every page. Provider outage must not silently extend privileged access or abruptly delete a school's data. Pilot plans can be assigned manually with audit history.

Test settings, jobs, APIs, SSE and exports, not only navigation. Cache capability decisions briefly only with safe invalidation/revocation bounds. Feature changes must be auditable and reversible without loss of records. Remove temporary flags after rollout stabilises.
