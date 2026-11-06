# Architecture decisions and open questions

Accepted planning decisions are requirements for implementation, not statements that infrastructure exists.

| ID | Decision | Reason / consequence |
|---|---|---|
| D01 | Next.js App Router + TypeScript + PostgreSQL | User-selected stack; portable to generic VPS |
| D02 | Modular monolith with separate worker | Shared domain code, independent durable work without microservice overhead |
| D03 | Shared database with school keys, constraints and RLS | Practical first deployment; isolation enforced beyond UI |
| D04 | PostgreSQL outbox/jobs; SSE plus HTTP | Durable changes with minimal required infrastructure; no mandatory Redis |
| D05 | Local database sessions with maintained auth library | Avoid hosted identity lock-in and custom security primitives |
| D06 | Drizzle/driver plus reviewed SQL migrations | Typed queries while retaining PostgreSQL-specific security/constraints |
| D07 | Private disk storage behind an adapter | Self-hosted first; future storage migration possible |
| D08 | In-app inbox is baseline delivery | External messaging is optional and failure must not erase school events |
| D09 | Immutable posted finance and published academic snapshots | Traceable corrections and reliable history |
| D10 | Modules use entitlements + settings + permissions | School-specific capability without weakening security |
| D11 | Subdomains first; verified custom domains later | Early tenant operation without premature certificate complexity |
| D12 | Keep current starter branch; completed is UI reference | Avoid merging tutorial assumptions into production foundation |

## Resolve in the specified step

| Question | Timing | Default until resolved |
|---|---|---|
| Maintained auth library and exact supported versions | 01–02 | No production login; document evaluation and pin dependencies |
| Upstream tutorial/asset licence | 01, before distribution | Preserve attribution, replace unlicensed inherited material before distribution |
| School name/product domain and brand | Before public onboarding | Generic development labels; no domain purchase assumed |
| Initial pilot school, calendar and guardian verification | 03/pilot | Synthetic data; staff-approved linking only |
| Absence grace, retrospective attendance and notification rules | 06 | Explicit unrecorded states; no invented automated sanctions |
| Forum/student messaging oversight and moderation | 08–09 | Private school spaces; moderation controls before opening discussion |
| Marks weights, coefficients, missing/exempt and ranking rules | 10 | No published calculations without a valid configured scheme |
| Provider, merchant ownership, settlement and refund responsibilities | 12 | Online collection off; no custody/pooled balances |
| Retention, consent/legal basis and school agreements | Before real data pilot | No claim of legal compliance; local verification required |
| Measured VPS size, recovery objectives and alert contacts | 13 | Targets in OPERATIONS are proposals; no capacity/SLA promise |
| Paid plans and commercial feature bundles | Before commercial billing | Manually assigned audited pilot entitlements |

Record new decisions with date, alternatives, tradeoffs and affected documents. Material changes to isolation, persistence, authentication or financial ownership require an explicit decision update rather than an incidental package addition.
