---
stepsCompleted: [1, 2, 3, 4, 5, 6, 7, 8]
lastStep: 8
status: 'complete'
completedAt: '2026-06-16'
inputDocuments:
  - _bmad-output/planning-artifacts/prds/prd-lds-intranet-2026-06-16/prd.md
  - _bmad-output/planning-artifacts/prds/prd-lds-intranet-2026-06-16/addendum.md
  - docs/README.md
  - docs/overview.md
  - docs/roadmap.md
  - docs/risks.md
workflowType: 'architecture'
project_name: 'lds-intranet'
user_name: 'Ifuensan'
date: '2026-06-16'
---

# Architecture Decision Document

_This document builds collaboratively through step-by-step discovery. Sections are appended as we work through each architectural decision together._

## Project Context Analysis

### Requirements Overview

**Functional Requirements:** 43 FRs across 8 feature groups (§4.1–4.8). v1 ("skeleton")
centers on: Identity & Access (FR-1–7a), Entry & Profile wizard (FR-8–12), Course
Submission incl. per-class content breakdown (FR-13–17a), Drafts save/resume (FR-18–22),
GitHub-PR Approval (FR-23–28), and Handoff to Moodle (FR-29–36a). Marketing/public-front
(FR-40–43) are deferred. Architecturally the FRs describe a **workflow/state-machine
application** (the submission status lifecycle: draft → submitted → changes-requested →
approved → published / rejected) wrapped around **three external integrations**:
the IdP, the GitHub `courses` repo, and Moodle (via the existing Python loader).

**Non-Functional Requirements:**
- NFR-1 (availability independence from Moodle) → async/queued handoff; no synchronous
  Moodle calls in the user request path.
- NFR-4 (one canonical identity across 3 methods + account linking + GitHub-author
  reconciliation) → the hardest data-model decision; build-blocking (OQ-4).
- NFR-3 + NFR-5 (durable audit trail + idempotent handoff) → persisted status
  transitions and safe Moodle re-runs.
- NFR-2 (data ownership/history) → lifecycle events captured from day one (FR-22)
  as the analytics foundation.
- NFR-6 (English UI), NFR-7 (auth hygiene + least-privilege RBAC across 4 roles).

**Scale & Complexity:**
Complexity is driven by **integration breadth and async orchestration with idempotency
guarantees**, NOT data volume (target ≈50 courses / 3 months). The intranet is an
integration hub and source-of-truth workflow engine more than a high-scale data app.

- Primary domain: Full-stack web app + backend integration/orchestration
- Complexity level: Medium–High (integration- and identity-driven)
- Estimated architectural components: ~7 (web UI/wizard, API/workflow core, identity/RBAC,
  GitHub integration, handoff orchestrator/queue, the Moodle loader service, notification
  + asset/storage services)

### Technical Constraints & Dependencies

- **Greenfield** application — no existing intranet code; stack is open (decided downstream).
- **Existing asset to reuse:** `moodle-course-loader` (Python) with proven
  `PlanBCourseBuilder` machinery; **missing** user/role/enrolment WS functions (added by P101).
- **External dependencies:** IdP (Authentik or GitHub-OIDC pilot), GitHub `courses` repo
  (submissions = PRs, approval = merge), Moodle 5.1 web-service API, customer.io.
- **Content contract:** Markdown COURSE_MASTER_PLAN (NOT intranet-emitted YAML; the loader
  derives internal YAML via the Claude API).

### Cross-Cutting Concerns Identified

- Identity federation + canonical-identity mapping + 4-role RBAC (least-privilege).
- Submission status lifecycle state machine kept in sync with GitHub PR state.
- Asynchronous handoff orchestration: trigger (PR merge) → doc generation → queued
  Moodle build → link write-back, with failure/retry + idempotency (FR-36 / NFR-5).
- Durable audit + lifecycle event capture from day one (NFR-3, FR-22).
- Notification fan-out (customer.io) + lead capture.
- Asset storage and reference resolution across intranet → GitHub-Markdown → Moodle.

## Starter Template Evaluation

### Primary Technology Domain

Full-stack web application (Next.js + TypeScript) acting as an integration hub, with a
separate Python loader service. Confirmed stack preferences: Next.js + TS, PostgreSQL +
Prisma, self-hosted via Docker/VPS.

### Starter Options Considered

- **create-t3-app (T3 Stack)** — Next.js + TypeScript + tRPC + Prisma + Auth.js (NextAuth)
  + Tailwind, all pre-wired. End-to-end type safety, ~28K★, actively maintained, the de-facto
  2026 standard for TS full-stack apps. Interactive CLI lets you select ORM (Prisma) and
  Auth. **Best fit** — matches the chosen stack 1:1 and its Auth.js layer covers the
  3-method identity requirement out of the box.
- **create-next-app (bare Next.js)** — official, minimal. Would require manually adding
  Prisma, Auth.js, tRPC/route structure, Tailwind. More control, more wiring; chosen
  components are identical to what T3 pre-assembles, so T3 saves the boilerplate.
- **Remix/SvelteKit starters** — viable full-stack options but rejected since the framework
  decision (Next.js) is made.

### Selected Starter: create-t3-app

**Rationale for Selection:**
Scaffolds the exact confirmed stack (Next.js App Router + TS + Prisma/Postgres + Auth.js +
Tailwind) with type-safe glue. Critically, Auth.js (NextAuth v5) provides native Authentik,
GitHub, and Google providers plus custom-OIDC support and a Prisma account model — the
foundation for federated identity (FR-1/2/6) and the canonical-identity data model (NFR-4).
tRPC gives a type-safe internal API for the wizard/dashboards; external webhooks
(GitHub PR events, customer.io) are implemented as Next.js route handlers.

**Initialization Command:**

```bash
npm create t3-app@latest lds-intranet
# Interactive selections: TypeScript · Tailwind CSS · tRPC · Auth.js (NextAuth)
# · Prisma · App Router · PostgreSQL (Prisma provider)
```

**Architectural Decisions Provided by Starter:**

- **Language & Runtime:** TypeScript (strict), Node.js, Next.js App Router.
- **Styling Solution:** Tailwind CSS.
- **API Layer:** tRPC (type-safe internal RPC); REST route handlers for external webhooks.
- **ORM / Database:** Prisma ORM targeting PostgreSQL; schema + migration workflow.
- **Auth:** Auth.js (NextAuth v5) with Prisma adapter; provider config for OIDC/OAuth.
- **Build Tooling:** Next.js build with `standalone` output (Docker-friendly).
- **Code Organization:** `src/` with `app/` routes, `server/api` (tRPC routers),
  `server/auth`, Prisma schema; env validation via `@t3-oss/env`.
- **Testing/Lint:** ESLint + Prettier preconfigured; test framework added as a follow-up.

> **Auth.js note (revisit in the Identity decision):** automatic account-linking is
> disabled by default; FR-2/FR-2a (one canonical identity across methods + existing-student
> fallback) requires an explicit linking strategy on top of the adapter.

**Note:** Project initialization using this command should be the first implementation story.

## Core Architectural Decisions

### Decision Priority Analysis

**Critical Decisions (Block Implementation):**
- Identity scope = full Authentik 3-method federation (resolves OQ-4 direction).
- Handoff execution = DB-backed job queue + worker (resolves FR-36 / NFR-5 mechanism).
- Artifact model & trigger = two Markdown docs in GitHub + `@course_bot` ChatOps trigger
  (refines OQ-3 and FR-33).

**Important Decisions (Shape Architecture):**
- GitHub integration via a GitHub App ("course_bot"); tRPC internal API + REST webhooks.
- Asset storage split: Markdown/small files in the `courses` repo; large objects in Garage (S3).
- End-to-end Zod validation; explicit persisted status state machine.

**Deferred Decisions (Post-MVP):**
- Multi-draft + fine-grained autosave (FR-21); marketing automation (Metricool/Canva);
  public front. Migration of existing instructors to canonical identity (FR-7).

### Data Architecture

- **Database:** PostgreSQL via Prisma (from starter). Single source of truth for users,
  identities/linked accounts, teacher profiles, submissions, drafts, status transitions,
  and the day-one lifecycle/audit event log (FR-22, NFR-2/3).
- **Validation:** Zod schemas shared between tRPC inputs and Prisma write paths
  (single validation definition, reused client + server).
- **Migrations:** Prisma Migrate, checked into the repo.
- **State machine:** `SubmissionStatus` enum (`draft → submitted → changes-requested →
  approved → published / rejected`) persisted on the submission; every transition writes a
  timestamped `LifecycleEvent` row. PR state is mirrored INTO this enum (intranet is source
  of truth), not the reverse.

### Authentication & Security

- **IdP:** Self-hosted **Authentik** as OIDC provider with three methods — local
  username/password, Google, GitHub (FR-1). Auth.js (NextAuth v5) is the client via the
  Authentik provider; Moodle 5.1 is a separate OIDC client. `wstoken` (Moodle web-service
  auth) is a distinct plane from the OIDC login token.
- **Canonical identity (NFR-4):** Auth.js Prisma adapter stores one `User` + multiple
  `Account` rows. Automatic linking is OFF by default — implement an explicit linking
  strategy keyed on verified email + an account-linking confirmation step; define the
  existing-student→teacher fallback (FR-2a) and GitHub-author reconciliation (FR-6) against
  the Authentik `sub`.
- **Authorization:** Four intranet roles (instructor, reviewer/Ops, marketing, admin)
  enforced as least-privilege checks in tRPC middleware per the PRD role×capability matrix.
- **Security:** Standard auth hygiene delegated to Authentik for federated methods; local
  password storage + session handling per NFR-7; secrets via environment/secret store.

### API & Communication Patterns

- **Internal API:** tRPC (type-safe) for the wizard and dashboards.
- **External webhooks:** Next.js route handlers for GitHub and customer.io events.
- **GitHub integration:** a **GitHub App** ("course_bot") with a webhook endpoint and
  least-privilege repo permissions; used to (a) open/manage PRs, (b) post bot comments,
  (c) receive `issue_comment` and `pull_request` events, and (d) (optional) verify
  GPG-signed commits. Preferred over a PAT for identity, scoping, and auditability.

### Artifact Pipeline & Handoff Flow (refines OQ-3 / FR-17/29/33)

Two separate Markdown artifacts live in the `courses` repo PR:
- **COURSE_MASTER_PLAN.md** — the full curriculum, **authored/edited by the instructor as
  Markdown directly in GitHub** after the wizard scaffolds it (consistent with FR-17a:
  intranet content goes read-only post-submission; GitHub becomes the editing surface).
- **MKT_BRIEFING.md** — marketing-oriented, **generated from the wizard submission data**
  and committed to the PR (the wizard metadata is its source).
- **`course.yml` companion** (resolves OQ-5) — machine-readable metadata
  (category, cohort, shortname, type) the handoff worker consumes; the reviewer-assigned
  Moodle category (FR-30a) is written here at approval.

**Trigger model (ChatOps):**
1. Instructor finishes editing the curriculum and comments **`@course_bot finished_course`**
   on the PR.
2. course_bot (GitHub App) receives the comment webhook → advances status to
   `submitted` (ready for review), **notifies Operations and Marketing** via customer.io
   (Marketing receives the MKT_BRIEFING early, per FR-40 intent).
3. Reviewer/Ops reviews; requests changes (comment-driven, `changes-requested`) or approves.
4. **Approval = merging the PR.** The merge webhook **enqueues a handoff job**; status → `approved`.

> **Interpretation (confirmed):** the `@course_bot finished_course` comment opens the
> *review* flow (and fires the Ops + Marketing notifications); the **PR merge** remains the
> *handoff/build* trigger (FR-33).

### Handoff Orchestration (FR-33/36, NFR-1/5)

- **Queue:** **pg-boss** (Postgres-backed; reuses the existing DB, no extra broker, has
  retries + dead-letter + dashboard). A worker process (same Node app or a sidecar) drains
  handoff jobs.
- **Flow:** merge webhook → enqueue `handoff(submissionId)` → worker generates final inputs,
  invokes the **moodle-course-loader** (separate Python service/CLI) → loader builds the
  course **hidden** in Moodle in the reviewer-assigned category, assigns Teacher role +
  enrolment (P101) → worker writes back the **Moodle link**, sets status `published`,
  notifies instructor (with link) + Ops.
- **Idempotency (NFR-5):** job keyed by submission + a deterministic Moodle shortname;
  loader re-runs are no-ops/updates, never duplicate-create. Failures retry with backoff,
  then dead-letter + Ops notification (FR-36); Moodle stays off the user request path (NFR-1).

### Frontend Architecture

- **Next.js App Router** with React Server Components; tRPC client for mutations/queries.
- **Wizard:** multi-step form with React Hook Form + Zod; per-step draft persistence to
  Postgres (FR-18, single-draft save/resume in v1).
- **Styling:** Tailwind CSS (from starter).

### Infrastructure & Deployment

- **Self-hosted via Docker Compose** on a VPS. Services: Next.js intranet (standalone
  output), PostgreSQL, **Authentik**, **Garage** (S3-compatible object store, v2.3.0),
  the **moodle-course-loader** worker/service (Python). Moodle is external.
- **Asset handling (FR-16a):** Markdown + small files committed to the `courses` repo;
  large objects (presentation videos, heavy source materials) uploaded to **Garage**, with
  stable object URLs/keys stored on the submission and embedded in the Markdown so the
  loader resolves and re-uploads them into Moodle at build time.
- **Config:** `@t3-oss/env` typed env validation; secrets via env/secret store.
- **CI/CD & monitoring:** GitHub-based CI for the intranet; pg-boss dashboard for job
  observability. (Detailed pipeline/monitoring deferred to deployment story.)

### Decision Impact Analysis

**Implementation Sequence:**
1. Project init (`create-t3-app`) + Docker Compose skeleton (Postgres, Authentik, Garage).
2. Identity & RBAC (Authentik + Auth.js, canonical identity + linking) — build-blocking.
3. **Spike the moodle-course-loader integration** (P101/PA/PC) before committing the handoff.
4. Wizard → submission → `courses` PR creation (course_bot GitHub App).
5. ChatOps trigger + status state machine + lifecycle events + customer.io notifications.
6. Handoff worker (pg-boss) → loader → Moodle build → write-back.

**Cross-Component Dependencies:**
- Canonical identity (Authentik `sub`) underpins RBAC, PR-author reconciliation, and audit.
- The status state machine is the spine: webhooks (bot comment, merge) write to it; the
  queue, notifications, and dashboards read from it.
- The `course.yml` companion couples the wizard metadata, reviewer's category choice, and
  the loader's build parameters.

## Implementation Patterns & Consistency Rules

### Pattern Categories Defined

**Critical Conflict Points Identified:** ~8 areas where AI agents could diverge —
DB naming, tRPC vs webhook conventions, the status-enum strings, lifecycle-event names,
identity/account vocabulary, error/result shape, validation placement, and async-job naming.
The overriding rule: **PRD §3 Glossary terms and the §4.5 status strings are used VERBATIM**
in code, DB values, event names, and UI — no synonyms, no re-casing.

### Naming Patterns

**Database (Prisma → PostgreSQL):**
- Prisma models: PascalCase singular (`User`, `Submission`, `TeacherProfile`,
  `LifecycleEvent`, `LinkedAccount`). Map to snake_case plural tables via
  `@@map("submissions")`; fields camelCase in Prisma, `@map("github_login")` to snake_case
  columns. Snake_case in the DB, camelCase in TS.
- Primary keys: `id` (cuid/uuid). Foreign keys: `<entity>Id` (e.g. `submissionId`,
  `userId`). Timestamps: `createdAt`, `updatedAt`.
- Enums in DB store the **exact PRD strings**: `SubmissionStatus` = `draft` | `submitted`
  | `changes-requested` | `approved` | `published` | `rejected`. `Role` = `instructor` |
  `reviewer` | `marketing` | `admin`.

**API (tRPC + webhooks):**
- tRPC routers grouped by domain: `submissionRouter`, `profileRouter`, `authRouter`,
  `handoffRouter`. Procedures named `verbNoun` camelCase: `createDraft`, `submitCourse`,
  `requestChanges`, `getSubmission`, `listSubmissions`.
- Webhook routes (REST) under `app/api/webhooks/<source>/route.ts`:
  `/api/webhooks/github`, `/api/webhooks/customerio`. Always verify signatures.
- No REST resource design needed beyond webhooks (tRPC is the app API).

**Code / Files:**
- Components: PascalCase files + names (`WizardStep.tsx`, `SubmissionStatusBadge.tsx`).
- Non-component files: kebab-case (`submission-state-machine.ts`, `course-bot.ts`).
- Functions/vars: camelCase. Types/interfaces/Zod schemas: PascalCase
  (`SubmissionInput`, `CourseMetadataSchema`). Constants: UPPER_SNAKE.
- Domain vocabulary in code matches the glossary: `COURSE_MASTER_PLAN`, `MKT_BRIEFING`,
  `coursesRepo`, `moodleLink`, `handoff` (never "publish job"/"export"/"sync").

### Structure Patterns

- **Feature-first** organization under `src/`: `src/features/<domain>/` holds that domain's
  components, hooks, and Zod schemas; cross-cutting server code stays in `src/server/`
  (tRPC routers in `src/server/api/routers/`, auth in `src/server/auth/`, queue/worker in
  `src/server/jobs/`). Prisma schema in `prisma/schema.prisma`.
- **Tests co-located**: `*.test.ts(x)` beside the file under test; e2e in `e2e/`.
- Shared utilities in `src/lib/`; shared Zod schemas in `src/lib/schemas/` (imported by both
  tRPC inputs and forms).
- Env access ONLY through `src/env.js` (`@t3-oss/env`); never `process.env` directly.

### Format Patterns

- **tRPC**: return domain objects directly (no `{data,error}` wrapper); errors thrown as
  `TRPCError` with a typed `code`. Client surfaces them via standard tRPC error handling.
- **Webhooks**: respond `2x` fast; do work async (enqueue), never block on Moodle.
- **Dates**: ISO-8601 UTC strings over the wire; `DateTime` in Prisma; format for display
  only in the UI layer.
- **JSON over the wire**: camelCase everywhere (TS-native). `course.yml` (machine metadata)
  uses snake_case keys to match loader/Moodle conventions — the one deliberate exception.
- Booleans real `true/false`; nullable fields explicitly `T | null`, not `undefined`.

### Communication Patterns

- **Lifecycle events** (FR-22) — table `LifecycleEvent`, `type` stored as dot.case past-tense
  verbatim: `wizard.started`, `step.reached`, `course.submitted`, `changes.requested`,
  `course.approved`, `course.published`, `course.rejected`, `handoff.started`,
  `handoff.failed`. Payload: `{ submissionId, actorId, at, meta }`. Append-only; never mutate.
- **Status transitions** go through ONE function (`transitionSubmission(id, toStatus, ctx)`)
  that validates the allowed transition (per the §4.5 table), writes the row, emits the
  lifecycle event, and triggers notifications. No ad-hoc status writes anywhere else.
- **Jobs** (pg-boss): queue names kebab-case (`course-handoff`, `briefing-generate`);
  payloads minimal + idempotency key (`submissionId`); handlers in `src/server/jobs/`.
- **State (frontend)**: server state via tRPC/React Query (no Redux); wizard form state via
  React Hook Form; immutable updates only.

### Process Patterns

- **Validation**: Zod at every boundary — form (client) AND tRPC input (server) reuse the
  same schema from `src/lib/schemas/`. Server never trusts client validation.
- **Error handling**: typed `TRPCError` server-side; React error boundaries per route
  segment; user-facing messages are friendly + actionable, internal detail goes to logs only.
- **Retry/idempotency**: only the handoff worker retries (pg-boss backoff → dead-letter +
  Ops notification). User-facing mutations do not silently retry.
- **Auth flow**: all protected procedures use tRPC `protectedProcedure` /
  `roleProcedure(role)` middleware; never re-implement auth checks inline.
- **Logging**: structured JSON logs with `submissionId`/`userId` correlation; levels
  `debug|info|warn|error`; never log secrets or `wstoken`.

### Enforcement Guidelines

**All AI Agents MUST:**
- Use PRD glossary terms and the exact status/role enum strings verbatim.
- Route every status change through `transitionSubmission` and every status change must emit
  a `LifecycleEvent`.
- Define each entity's Zod schema once in `src/lib/schemas/` and reuse it client + server.
- Access env only via `src/env.js`; verify all webhook signatures.
- Keep Moodle calls out of the request path — handoff work happens only in pg-boss jobs.

**Pattern Enforcement:** ESLint + Prettier + `tsc --noEmit` in CI; Prisma migrations
reviewed in PRs; a short "conventions" section in the repo README mirrors this list.

### Pattern Examples

**Good:** `transitionSubmission(id, "changes-requested", ctx)` → writes row + emits
`changes.requested` + notifies instructor.
**Anti-pattern:** `await prisma.submission.update({ data: { status: "ChangesRequested" }})`
(wrong casing, no event, no transition guard).
**Good:** `CourseMetadataSchema` imported by both the wizard form and `submitCourse` input.
**Anti-pattern:** separate hand-written validation in the form and a different check on the server.

## Project Structure & Boundaries

### Complete Project Directory Structure

```
lds-intranet/                         # Next.js (T3) intranet — source of truth
├── README.md                         # incl. "Conventions" section mirroring step 5
├── package.json
├── next.config.js                    # output: 'standalone' (Docker)
├── tailwind.config.ts
├── tsconfig.json
├── .env.example                      # all keys; real values via secrets
├── docker-compose.yml                # intranet + postgres + authentik + garage + loader
├── Dockerfile                        # multi-stage, standalone output
├── .github/
│   └── workflows/
│       └── ci.yml                    # lint + tsc --noEmit + test
├── prisma/
│   ├── schema.prisma                 # User, LinkedAccount, TeacherProfile,
│   │                                 #   Submission, Draft, LifecycleEvent, HandoffJob
│   └── migrations/
├── src/
│   ├── env.js                        # @t3-oss/env — ONLY env access point
│   ├── middleware.ts                 # auth/session gate
│   ├── app/
│   │   ├── layout.tsx
│   │   ├── page.tsx                  # "Teach on LdS" state-aware entry (FR-8/9)
│   │   ├── (auth)/                   # sign-in screens (Authentik/Auth.js)
│   │   ├── dashboard/                # instructor + Ops dashboards (FR-9/20/24)
│   │   ├── wizard/                   # profile → metadata → content steps (FR-10..16)
│   │   └── api/
│   │       ├── auth/[...nextauth]/route.ts
│   │       ├── trpc/[trpc]/route.ts
│   │       └── webhooks/
│   │           ├── github/route.ts        # course_bot events (FR-23..27, ChatOps)
│   │           └── customerio/route.ts
│   ├── features/                     # feature-first (UI + hooks + schemas per domain)
│   │   ├── identity/                 # §4.1 — login, linking, roles (FR-1..7a)
│   │   ├── profile/                  # §4.2 — teacher profile wizard (FR-10..12)
│   │   ├── submission/               # §4.3 — metadata + content breakdown (FR-13..17a)
│   │   ├── drafts/                   # §4.4 — save/resume (FR-18..22)
│   │   ├── approval/                 # §4.5 — PR status mirror, review UI (FR-23..28)
│   │   ├── handoff/                  # §4.6 — status, Moodle-link surfacing (FR-29..36a)
│   │   └── notifications/            # §4.7 — customer.io triggers, lead capture (FR-37..39)
│   ├── server/
│   │   ├── api/
│   │   │   ├── trpc.ts               # protectedProcedure / roleProcedure middleware
│   │   │   └── routers/              # submissionRouter, profileRouter, authRouter, handoffRouter
│   │   ├── auth/                     # Auth.js config, Authentik provider, linking strategy
│   │   ├── db.ts                     # Prisma client singleton
│   │   ├── submission/
│   │   │   └── state-machine.ts      # transitionSubmission() — single status authority
│   │   ├── github/
│   │   │   └── course-bot.ts         # GitHub App: open PR, comments, parse @course_bot cmd
│   │   ├── moodle/
│   │   │   └── loader-client.ts      # invokes the loader service; reads back Moodle link
│   │   ├── artifacts/
│   │   │   └── briefing.ts           # MKT_BRIEFING generation from submission data (PA)
│   │   ├── storage/
│   │   │   └── garage.ts             # S3 client for large assets (FR-16a)
│   │   └── jobs/
│   │       ├── boss.ts               # pg-boss init
│   │       ├── course-handoff.ts     # PB/PC orchestration, idempotent (FR-33/36)
│   │       └── briefing-generate.ts
│   ├── lib/
│   │   ├── schemas/                  # shared Zod schemas (client + server)
│   │   └── utils.ts
│   └── styles/globals.css
├── e2e/                              # Playwright end-to-end
└── public/

moodle-course-loader/                 # SEPARATE repo/service (Python) — reused as-is
                                       # PlanBCourseBuilder + planb_source; +P101 WS funcs

courses/                              # SEPARATE GitHub repo — submissions live as PRs
└── <slug>/
    ├── COURSE_MASTER_PLAN.md         # instructor-authored curriculum
    ├── MKT_BRIEFING.md               # generated, marketing-oriented
    ├── course.yml                    # machine metadata (category, cohort, shortname)
    └── assets/                       # small files; large objects → Garage
```

### Architectural Boundaries

**API Boundaries:**
- Internal: tRPC routers (`/api/trpc`) — the only app API for UI; role-gated middleware.
- External in: signed webhooks (`/api/webhooks/github`, `/customerio`) — verify, enqueue, return fast.
- External out: GitHub (Octokit via course-bot App), Moodle (loader-client → loader → WS API),
  customer.io (notifications client), Garage (S3 client). Moodle is reachable ONLY from
  pg-boss jobs, never the request path (NFR-1).

**Component Boundaries:**
- `features/*` own their UI + form schemas; they call tRPC, never Prisma directly.
- `server/*` owns all data + integrations; `state-machine.ts` is the sole writer of
  `Submission.status` and the sole emitter of `LifecycleEvent`.

**Service Boundaries:**
- Three deployable units in Compose: the Next.js app (web + worker can be one or split),
  the Python loader, and infra (Postgres, Authentik, Garage). Moodle is external.

**Data Boundaries:**
- Intranet Postgres is source of truth for identity, profiles, submissions, drafts, events.
- `courses` repo is the source of truth for course CONTENT post-submission (FR-17a).
- Moodle owns content post-publish (FR-36a). Garage holds large binary assets.

### Requirements to Structure Mapping

| PRD area | Lives in |
|---|---|
| §4.1 Identity & Access (FR-1..7a) | `features/identity/`, `server/auth/`, Prisma `User`/`LinkedAccount`/`Role` |
| §4.2 Entry & Profile (FR-8..12) | `app/page.tsx`, `app/wizard/`, `features/profile/` |
| §4.3 Course Submission (FR-13..17a) | `features/submission/`, `lib/schemas/`, `server/github/` |
| §4.4 Drafts (FR-18..22) | `features/drafts/`, Prisma `Draft`/`LifecycleEvent` |
| §4.5 Approval via PR (FR-23..28) | `features/approval/`, `server/github/course-bot.ts`, webhook route |
| §4.6 Handoff (FR-29..36a) + PA/PB/PC/P101 | `server/jobs/course-handoff.ts`, `server/moodle/`, `server/artifacts/`, loader repo |
| §4.7 Notifications & leads (FR-37..39) | `features/notifications/`, `server/` customer.io client |
| §4.8 Marketing (FR-40..43) | deferred — not scaffolded in v1 |

**Cross-cutting:** auth/RBAC → `server/api/trpc.ts` + `middleware.ts`; status state machine →
`server/submission/state-machine.ts`; events/audit → `LifecycleEvent` (append-only).

### Integration Points

- **Internal:** UI → tRPC → server services → Prisma. Status changes → `transitionSubmission`
  → event + notification + (on merge) job enqueue.
- **External:** course_bot (GitHub App) ↔ `courses` repo PRs; loader-client → Python loader →
  Moodle WS API; customer.io for email/leads; Garage for assets.
- **Data Flow:** wizard → Draft → Submission + PR (course_bot) → instructor edits Markdown in
  GitHub → `@course_bot finished_course` → review → merge → `course-handoff` job →
  artifacts + loader build (hidden) → Moodle link write-back → `published`.

### File Organization Patterns

- **Config:** root-level (`next.config.js`, `tailwind.config.ts`, `docker-compose.yml`);
  env only via `src/env.js` + `.env.example`.
- **Source:** feature-first under `src/features/`; all server/integration code under
  `src/server/`; shared Zod schemas in `src/lib/schemas/`.
- **Tests:** unit/component co-located (`*.test.ts(x)`); e2e in `e2e/`.
- **Assets:** app static in `public/`; instructor uploads in Garage; small course files in
  the `courses` repo `assets/`.

### Development Workflow Integration

- **Dev:** `docker compose up` brings Postgres + Authentik + Garage; `npm run dev` runs the
  Next.js app; loader runnable standalone for the integration spike.
- **Build:** Next.js standalone output → single Docker image; Prisma migrate on deploy.
- **Deploy:** Compose on the VPS; the pg-boss worker runs as the same image with a worker
  entrypoint (or a sibling service); secrets injected via env.

## Architecture Validation Results

### Coherence Validation ✅

**Decision Compatibility:** The stack is internally consistent — create-t3-app bundles
Next.js + TS + tRPC + Prisma + Auth.js + Tailwind as a tested combination; pg-boss reuses
the same PostgreSQL instance (no extra broker); Garage v2.3.0 is a drop-in S3 client target;
Authentik is an OIDC provider Auth.js v5 supports natively. No version conflicts. The Python
loader stays an isolated service, so its runtime never collides with the Node stack.

**Pattern Consistency:** Naming/format/process patterns are stack-idiomatic and reinforce the
decisions: the single `transitionSubmission` authority + append-only `LifecycleEvent` directly
serve NFR-3 (audit) and FR-22 (events); "Moodle only from jobs" enforces NFR-1; shared Zod
schemas enforce the validation rule. No contradictions between patterns and decisions.

**Structure Alignment:** The tree realizes the decisions — `server/submission/state-machine.ts`
is the lone status writer; `server/jobs/` isolates handoff orchestration off the request path;
`features/*` → tRPC → `server/*` → Prisma respects the layering; webhook routes verify-and-enqueue.

### Requirements Coverage Validation ✅ (with noted open items)

**Functional Requirements Coverage:**
- §4.1 Identity (FR-1..7a): ✅ Authentik 3-method + Auth.js + canonical-identity model + RBAC
  middleware. ⚠️ FR-2a/FR-6 linking *strategy* decided (explicit, email-keyed) but the detailed
  mechanism needs design.
- §4.2 Entry/Profile (FR-8..12): ✅ state-aware entry + wizard + editable profile.
- §4.3 Submission (FR-13..17a): ✅ metadata/content schemas, package → PR, read-only-after-submit.
  ⚠️ FR-16a asset type/size limits TBD with the team.
- §4.4 Drafts (FR-18..22): ✅ single-draft save/resume + lifecycle events; multi-draft deferred.
- §4.5 Approval (FR-23..28): ✅ course_bot GitHub App, status mirror, ChatOps trigger, OQ-5 → `course.yml`.
- §4.6 Handoff (FR-29..36a): ✅ trigger/queue/idempotency/write-back DESIGNED. ⚠️ the
  loader-internal mapping (PA/PB/PC/P101) is undefined pending the spike (OQ-6).
- §4.7 Notifications (FR-37..39): ✅ customer.io transactional + lead capture.
- §4.8 Marketing (FR-40..43): ✅ correctly deferred; no v1 architecture owed.

**Non-Functional Requirements Coverage:**
- NFR-1 ✅ async handoff, Moodle off request path. NFR-2 ✅ Postgres SoT + retained events.
- NFR-3 ✅ PR trail + append-only events. NFR-4 ✅ canonical User + linked Accounts (mechanism
  detail pending). NFR-5 ✅ idempotent job keyed by submission/shortname. NFR-6 ✅ English UI.
  NFR-7 ✅ Authentik + local hygiene + least-privilege tRPC middleware.

### Implementation Readiness Validation

**Decision Completeness:** Critical decisions documented with verified versions (pg-boss 12.19.1,
Garage 2.3.0, Auth.js v5, T3). **Structure Completeness:** concrete tree, boundaries, and FR→dir
mapping present. **Pattern Completeness:** naming/format/communication/process patterns + examples
+ enforcement defined.

### Gap Analysis Results

**Critical (must de-risk before the handoff slice):**
- **G1 — Platform track undefined (OQ-6).** PA/PB/PC/P101 internals (COURSE_MASTER_PLAN →
  `PlanBCourseBuilder` schema mapping, exact WS functions for role/enrolment, asset resolution,
  mid-build failure recovery) are not yet specified. The *orchestration* is designed; the
  *loader contract* needs the spike. Blocks §4.6 implementation only — not §4.1–4.5.

**Important:**
- **G2 — Identity linking mechanism (FR-2a/FR-6).** Strategy chosen; the exact flow
  (email-match confirmation UX, existing-student fallback, GitHub-author↔`sub` reconciliation)
  needs a short design before building §4.1/§4.5.
- **G3 — Asset constraints (FR-16a).** Accepted types + size/count limits TBD with the team.

**Nice-to-have:**
- **G4** Notification template catalog + customer.io event taxonomy.
- **G5** Worker deployment shape (in-process vs sibling container) — decide at deploy time.

### Validation Issues Addressed

G1 is consciously sequenced as the **first implementation action (a spike)**, per the PRD/roadmap;
it is a planned de-risking step, not a missing architectural decision. G2/G3 are scoped to their
owning feature and do not block project init, identity scaffolding, or the wizard.

### Architecture Completeness Checklist

**Requirements Analysis**
- [x] Project context thoroughly analyzed
- [x] Scale and complexity assessed
- [x] Technical constraints identified
- [x] Cross-cutting concerns mapped

**Architectural Decisions**
- [x] Critical decisions documented with versions
- [x] Technology stack fully specified
- [x] Integration patterns defined
- [x] Performance considerations addressed *(low-volume system; async handoff is the key perf/availability lever)*

**Implementation Patterns**
- [x] Naming conventions established
- [x] Structure patterns defined
- [x] Communication patterns specified
- [x] Process patterns documented

**Project Structure**
- [x] Complete directory structure defined
- [x] Component boundaries established
- [x] Integration points mapped
- [x] Requirements to structure mapping complete

### Architecture Readiness Assessment

**Overall Status:** READY WITH MINOR GAPS — all 16 checklist items confirmed; G1 (platform-track
spike) is an open critical-path *action* that gates only the handoff slice, with §4.1–4.5
implementable immediately.

**Confidence Level:** Medium-High — high for the intranet core (identity, wizard, submission,
approval); medium for the handoff until the loader spike validates the chosen contract.

**Key Strengths:**
- Single source-of-truth status machine + append-only events give clean auditability and analytics.
- Async, idempotent, queue-based handoff isolates the riskiest dependency and satisfies NFR-1/5.
- Stack maps 1:1 to requirements (Auth.js↔3-method identity; tRPC+Zod↔type-safe wizard).
- Clear service boundaries; the proven Python loader is reused rather than rebuilt.

**Areas for Future Enhancement:**
- Authentik instructor migration (FR-7); marketing automation + public front (§4.8);
  multi-draft/autosave (FR-21); post-publish analytics pipeline; bidirectional Moodle reconciliation.

### Implementation Handoff

**AI Agent Guidelines:**
- Follow all architectural decisions exactly as documented.
- Use implementation patterns consistently; status changes ONLY via `transitionSubmission`.
- Respect project structure and boundaries; keep Moodle calls inside pg-boss jobs.
- Refer to this document for all architectural questions.

**First Implementation Priority:**
1. **Spike the moodle-course-loader integration** (resolves G1) — highest priority.
2. In parallel, scaffold: `npm create t3-app@latest lds-intranet` + Docker Compose
   (Postgres + Authentik + Garage), then build Identity & RBAC (§4.1).
