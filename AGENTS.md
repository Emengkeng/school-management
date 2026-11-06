# Instructions for implementation agents

Read [docs/README.md](docs/README.md), [docs/STATUS.md](docs/STATUS.md), and [docs/AI-WORKFLOW.md](docs/AI-WORKFLOW.md) before changing anything. Read the architecture, security, data model and relevant workflow specifications before implementation.

## Scope and constraints
- Next.js App Router, TypeScript and PostgreSQL; deployable on a generic Linux VPS.
- Current repository is a tutorial UI, not an implemented school platform. Do not describe planned features as working.
- Default branch is `starter`; `origin/completed` is a UI reference, not a production backend. Do not merge it wholesale or replace unrelated work.
- Implement only the next eligible roadmap step unless the user explicitly selects a different scope. Finish dependencies first.
- Do not add managed-service dependencies, payment providers, public hosting, or deployments without a task that needs them. Open-source libraries are allowed; keep the runtime portable.
- Preserve tenant isolation, relationship permissions, audit history and transactional integrity. Neither a hostname nor a role label alone grants access.
- Read docs/ROLE-MANAGEMENT.md and docs/STUDENT-IDENTITY.md before changing identity, roles, student intake or transfers. Global matricule uniqueness does not grant cross-school record access.
- Never use production/student data in fixtures, logs or screenshots. Do not commit secrets.
- Follow current official documentation when selecting dependency versions. Existing Next.js 14.2.5 is inherited tutorial state, not an approved production baseline.
- Do not rewrite git history or change default branch merely to implement this plan.

## Definition of done for each step
1. Meet its acceptance criteria and applicable edge-case checks.
2. Run relevant automated checks and explain any unavailable verification; never mark an unverified claim as passed.
3. Update STATUS, affected documentation, and the edge-case register for newly discovered cases.
4. Report changes, checks, risks and the exact next step. Commit/push when instructed; a request to implement a phase does not itself authorise production deployment.
5. Keep a step in progress if any required check is blocked. Do not silently skip security, failure handling or data migration checks.

Documentation represents decisions and requirements, not executable configuration. Do not invent commands, credentials, providers or production addresses that do not exist.
