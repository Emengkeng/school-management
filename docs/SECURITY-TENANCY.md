# Identity, tenancy, domains and permissions

## Isolation contract

Every school-owned record has a non-null school ID. Relational references include school consistency through composite keys/foreign keys where applicable. School membership, guardian links, teacher assignments, active enrolment and permission scope determine access, not merely knowledge of an ID.

Application checks and PostgreSQL row-level security provide separate barriers. Use a non-owner runtime role without superuser/BYPASSRLS privileges; force row security on protected tables where appropriate. Keep migration and backup credentials outside the application. Configure transaction-local tenant context on the same connection used for every tenant query; never leave session-level tenant settings in a pooled connection. Missing context denies access. Test rollback, connection reuse and background jobs across tenants.

RLS is defence in depth, not proof that untrusted arbitrary SQL would be safe. The trusted server establishes validated context. Restrict database connectivity and test security-definer functions, views and maintenance paths explicitly. Worker jobs access one tenant at a time; platform discovery uses narrowly scoped routines/roles rather than unrestricted school-table access in ordinary requests.

Global tables (accounts, tenant registry, verified hostnames, platform subscriptions) need their own explicit access rules. Global uniqueness errors must not reveal private membership information. School-scoped uniqueness includes school ID. UUIDs reduce guessability but do not authorise access.

## Authentication and membership

Use local database-backed sessions; secure, HttpOnly cookies, CSRF/origin protection, password hashing through reviewed libraries, expiring recovery/invitation tokens stored hashed, throttling and session revocation. Prefer host-only cookies. Do not share a broad parent-domain session cookie with every school subdomain. Use a carefully reviewed one-time login exchange if cross-domain SSO is later required; otherwise separate sessions per hostname are acceptable.

A person can be teacher in A and guardian in B. Server resolution verifies both current membership and requested school for every request. Disabled memberships immediately block new actions. Remove pending offline actions from automatic sync until the account is revalidated.

Guardian links are school-approved, child-specific, effective-dated and revocable. Supporting multiple guardians does not imply every guardian has identical financial, messaging or academic access. Student enrolment and staff assignment are effective-dated; preserve history after departure without granting ongoing access by default.

## Permission examples

| Action | Required relationship/authority |
|---|---|
| Start lesson | Assigned teacher or authorised substitute for that occurrence |
| Edit attendance | Assigned recorder; closed-period correction requires review permission |
| View child report | Active permitted guardian link and published report |
| Enter marks | Assigned assessment/subject scope |
| Publish report cards | Explicit school publishing permission, approved results |
| Record payment | School finance permission |
| Refund payment | Explicit refund approval, available refundable amount |
| View private conversation | Participant or explicitly authorised oversight role with disclosed policy |
| Support access | Limited, expiring operator grant with reason and audit |

Avoid requiring the original author to approve their own high-impact change. Schools can configure approval staffing, but published marks and money retain traceable review/correction workflows.

## Domains

Platform-owned subdomain → verified school mapping → membership check. A verified custom hostname is an additional address for the same school, never a new tenant. Normalise hostname, strip ports safely, allowlist known hosts and trust forwarded host headers only from the configured reverse proxy. Reject unknown hosts, even if DNS points to the VPS.

Domain state: requested → ownership challenge → verified → TLS ready → active → suspended/removed. Verify ownership before routing school content or issuing tenant-controlled redirects. Reserve platform names. Rate-limit certificate requests and allow them only for eligible verified mappings. Avoid unrestricted on-demand TLS. Remove stale mappings, verify reassignment, preserve the platform subdomain fallback and audit changes. DNS verification alone does not bypass login.

## Data safety

No student content is public by default. Escape forum text and sanitise supported rich text; no arbitrary HTML. Validate uploads, scan before serving, restrict MIME/type/size and prevent path traversal. Logs exclude passwords, tokens, uploaded document bodies, full chat content and unnecessary student details. Audit includes actor, tenant, action, target, timestamps, reason and safe change metadata; restrict access and retention. Never record credentials in audits.

Before a real-school pilot, document applicable Cameroon privacy/child-data requirements, school agreements, authorisation/consent basis, retention, incident contacts and access/export/deletion procedures. This plan does not assert legal compliance. Obtain school policy decisions before enabling private messaging and guardian/student access.
