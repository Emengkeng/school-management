# School Management Platform

A planned multi-school platform for secondary and high schools in Cameroon: administration, lessons, attendance, learning resources, parent communication, marks, report cards, fees and payments. Each school has isolated records, configurable modules and a platform subdomain or verified custom domain.

**Current state:** documentation and tutorial frontend only. Authentication, PostgreSQL persistence, tenant isolation and the business workflows are not implemented.

## Start here

Read [the documentation index](docs/README.md), [implementation status](docs/STATUS.md) and [the AI handoff guide](docs/AI-WORKFLOW.md).

To continue with an AI, use:

> Read AGENTS.md, docs/README.md, docs/STATUS.md and docs/AI-WORKFLOW.md. Follow the next-step procedure, inspect the current code, and implement the next eligible step in docs/ROADMAP.md. Complete its acceptance checks, update documentation and report the next step. Do not deploy.

## Repository branches

- `starter`: current default branch; originally a placeholder homepage, menu definitions and sample data. This is where the planning documentation begins.
- `completed`: fuller tutorial UI with role dashboards, lists, calendars and forms. It still uses hardcoded roles/sample records, placeholder sign-in and form submissions that log data. Reuse selected presentation components only after establishing the application foundation.

Original reference: [Lama Dev](https://youtube.com/lamadev). Verify upstream licensing and asset permissions before distributing inherited material; no licence file was found in either inspected branch. Do not infer commercial permission from repository visibility or package metadata.

## Existing tutorial commands

The current package defines `npm run dev`, `npm run build`, `npm start` and `npm run lint`. Install using the committed lockfile (`npm ci`) when development starts. These commands run the inherited frontend, not the planned database/worker platform. Future setup instructions must be added when implemented.

## Direction

Next.js + TypeScript + PostgreSQL, a separate Node worker, private file storage and a reverse proxy on a Linux VPS. No dependency on Vercel, Supabase, Firebase, Clerk or hosted realtime infrastructure is required. See [architecture](docs/ARCHITECTURE.md).
