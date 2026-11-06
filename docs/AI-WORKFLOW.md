# AI implementation and handoff guide

## One prompt to continue

> Read AGENTS.md, docs/README.md, docs/STATUS.md and docs/AI-WORKFLOW.md. Inspect the current git state and actual implementation. Select the earliest incomplete, unblocked step whose dependencies pass in docs/ROADMAP.md. Read its referenced architecture, data model, security, workflows, features, edge cases and testing requirements. Implement only that step or one clearly bounded substep if it is too large. Run meaningful checks, update STATUS and affected documentation, and report evidence, blockers and the exact next step. Do not deploy. Do not treat planned documentation as implemented functionality.

## Explicit step prompt

> Read AGENTS.md and docs/STATUS.md. Implement step [NUMBER] in docs/ROADMAP.md after verifying its dependencies. Read all documents referenced by that step and the applicable cases in docs/EDGE-CASES.md. Preserve Next.js/PostgreSQL/VPS portability. Finish the acceptance checks, update the handoff, and explain any remaining blocker. Do not broaden scope or deploy.

Replace [NUMBER] with 01–13. To start this project, use 01. To request a commit/push, append: “Commit and push the verified changes to the current agreed branch; do not deploy.”

## Read/review prompt

> Review the implementation of step [NUMBER] against docs/ROADMAP.md, docs/SECURITY-TENANCY.md, docs/EDGE-CASES.md and docs/TESTING.md. Inspect actual code and tests; do not rely only on STATUS. Report concrete gaps with file references and their impact. Do not modify files unless asked.

## Resume after interruption

> Read AGENTS.md and docs/STATUS.md, inspect git diff/status and the last relevant commit. Continue the recorded in-progress substep without discarding existing work. Re-run only the checks needed for changed or unverified behaviour. Update the handoff and identify the next eligible step.

## Per-step execution

1. Read status, inspect branch/diff and verify previous evidence against current code. Preserve unrelated user work.
2. Identify required inputs and policies. Continue independent reversible work if an optional provider/policy is missing; record the dependent action as blocked.
3. List bounded deliverables, failure cases and meaningful checks before editing.
4. Implement domain behaviour, permissions, storage/data migrations and UI together where required. Do not create a dashboard that claims unsupported backend behaviour.
5. Exercise success, invalid input, unauthorised access, school boundary, retries/conflicts and relevant edge cases.
6. Record actual checks/results, new commands and schema changes. Add newly discovered cases and decisions.
7. Update status truthfully; a blocked acceptance criterion means the step is not done. End with the next executable instruction.

## Required STATUS handoff fields

Step/substep; completed behaviour; files/commit; verification commands and outcomes; edge-case IDs covered; outstanding gaps; decisions; next action. Never include credentials or private student data.

## When to ask versus proceed

Choose routine reversible implementation details consistently with this design and record them. Ask for essential unresolved business inputs before enabling their irreversible effects: actual grading rules, live payment provider/settlement ownership, guardian verification policy, legal/retention requirements or deployment credentials. Do not repeatedly ask to perform already authorised work. No production data or payment is necessary to test foundations.

## Documentation maintenance

STATUS describes reality; ROADMAP describes remaining work; DECISIONS records accepted choices and open questions. On scope changes update all affected contracts, not just a checklist. Keep stable edge-case IDs. Avoid silently changing money/grade/security semantics to make tests pass. Documentation-only changes need link/format/diff verification; application tests are required only when application behaviour changes.
