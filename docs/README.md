# Documentation index

These documents are the implementation contract. They describe planned behaviour unless STATUS explicitly records implementation and evidence. Planning baseline: 2026-11-06.

| Document | Purpose |
|---|---|
| [PRODUCT.md](PRODUCT.md) | Scope, users, module boundaries and rollout priorities |
| [ARCHITECTURE.md](ARCHITECTURE.md) | Components, transactions, realtime, storage and technical decisions |
| [REPORT-CARDS.md](REPORT-CARDS.md) | School-supplied DOCX templates, bulk exports and print workflow |
| [SCALE.md](SCALE.md) | Million-student design target, capacity gates and staged infrastructure |
| [ROLE-MANAGEMENT.md](ROLE-MANAGEMENT.md) | Scoped/custom roles, delegation, separation of duty and revocation |
| [STUDENT-IDENTITY.md](STUDENT-IDENTITY.md) | Global matricules, national identifiers, linking and authorised transfers |
| [DATA-MODEL.md](DATA-MODEL.md) | Entities, relationships, constraints and history |
| [SECURITY-TENANCY.md](SECURITY-TENANCY.md) | School isolation, identity, permissions and domains |
| [WORKFLOWS.md](WORKFLOWS.md) | Ordered school, teaching, parent, academic and finance flows |
| [FEATURES.md](FEATURES.md) | Entitlements, configuration, flags and module lifecycle |
| [EDGE-CASES.md](EDGE-CASES.md) | Failure scenarios, expected handling and verification |
| [OPERATIONS.md](OPERATIONS.md) | Local setup targets, VPS deployment, backups and incidents |
| [TESTING.md](TESTING.md) | Quality gates, negative tests and pilot readiness |
| [ROADMAP.md](ROADMAP.md) | Ordered implementation steps and acceptance criteria |
| [AI-WORKFLOW.md](AI-WORKFLOW.md) | Copyable continuation prompts and handoff procedure |
| [STATUS.md](STATUS.md) | Actual progress, evidence, blockers and next step |
| [DECISIONS.md](DECISIONS.md) | Architecture decisions and unresolved choices |

Reading order: STATUS → PRODUCT → ARCHITECTURE → SECURITY-TENANCY → DATA-MODEL → the relevant workflow/feature/edge cases → ROADMAP and TESTING. Agents must also follow root AGENTS.md.

## Terminology

- Tenant: a school, not a campus, hostname or individual user.
- Membership: a user's school-specific role/permissions; a global account may have multiple memberships.
- Lesson occurrence: one dated lesson; distinct from a recurring timetable rule.
- Attendance: a recorded observation about a student; distinct from a teacher reporting a lesson started.
- Platform subscription: the school pays the platform. Student fees: guardians pay the school. Keep these separate.
- Real time: committed updates reach connected authorised users promptly; no promise of immediate delivery while offline.

## Coverage limits

The edge-case register is extensive but not exhaustive. Add cases discovered in implementation, testing, incidents and school pilots. Do not interpret silence as authorisation to expose data or change financial/academic history. Resolve routine reversible design choices; document material unknowns before the dependent production capability is enabled.
