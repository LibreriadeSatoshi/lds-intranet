---
id: "01"
title: '"Teach on Librería de Satoshi" entry point'
track: product
status: draft
mvp: true
owner: TBD
depends_on: ["00"]
risk: low
jira: ENG-273
---

# Story 01 — "Teach on Librería de Satoshi" entry point

**Tasks:** [implementation breakdown →](tasks/01-entry-point.tasks.md)

## User story

**As** a logged-in user, **I want** an easy & smooth way to start teaching, **so that** I can
begin submission without hunting for it.

## Acceptance criteria

- **Visible entry point** — A "Become a Teacher" action in a consistent, documented location.
- **Routes to onboarding** — When clicked, I land on the page that starts the
  [Story 02](02-teacher-profile-wizard.md) profile + course creation + submission flow.
- **State-aware behavior:**
  - Not logged in → login (delegates to [Story 00](00-federated-identity.md)).
  - Logged in, no submission → new draft.
  - Has draft(s) → submissions dashboard.
  - Already an instructor → instructor dashboard.
- **Discoverable to the right people** — Specify who sees it.

## Intranet ↔ Moodle role mapping (closed)

- Intranet manages its own roles: instructor, reviewer/Ops, marketing.
- Moodle manages its own: Student, Teacher, Manager, admin.
- **reviewer/Ops** (intranet) → **Manager** (Moodle) for those who manage courses; some staff
  are **already admin** in Moodle. Manager and admin are managed *in Moodle*.
- **instructor** (intranet) → **Teacher / editing teacher** (Moodle), assigned by the loader
  on publish ([P101](../platform/P101-moodle-user-role-enrolment.md)).
- **Student** (Moodle) — course students.
- The Moodle **admin role is orthogonal** to the intranet: not read or managed from it.
- Not needed for this flow: Course creator (creation done by the loader via API),
  Non-editing teacher, Guest.
- Principle: the intranet authorizes intranet things; Moodle authorizes Moodle things. The
  IdP provides identity only.

## Notes

- Intranet-native entry point (no longer Moodle's "Request a course").
- With multiple drafts possible ([Story 04](04-save-resume-drafts.md)), "has draft" leads to
  the dashboard, not to a single draft.
- Authentication delegates to [Story 00](00-federated-identity.md).
- Entry to the intranet is via the public landing on Librería's site ("Become a Teacher" CTA)
  → login.

## Open questions

- **Discoverable to the right people** — who exactly sees the entry point?
