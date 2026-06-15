---
id: "07"
title: Handoff to Moodle, publication & instructor profile
track: product
status: draft
mvp: true
owner: TBD
depends_on: ["06", "PA", "PB", "PC", "P101"]
risk: high
jira: TBD
---

# Story 07 — Handoff to Moodle, publication, and instructor profile

> **Critical path.** This is the only point where real value is delivered (a live course in
> Moodle), and it depends on the *entire* platform track (PA/PB/PC/P101), which is currently
> undefined. Spike the integration before committing to dates. See [roadmap.md](../roadmap.md).

## User story

**As** an approved instructor, **I want** my course published and my profile marked as an
instructor, **so that** students can find me.

## Acceptance criteria

- **Document generation** — On approval, generation of the `MKT_BRIEFING` and the
  `COURSE_MASTER_PLAN` ([PA](../platform/PA-document-generation.md)) is triggered following
  the temporal sequence ([PB](../platform/PB-handoff-sequence.md)).
- **Handoff** — When the `COURSE_MASTER_PLAN` is ready, its reference enters the Sheet the
  loader processes; the loader builds the course ([PC](../platform/PC-loader-build-course.md)).
- **Handoff confirmation** — The intranet records the Moodle Link (write-back) and reflects
  the course as published.
- **Instructor role (intranet)** — With ≥1 approved course, the profile shows the instructor
  indicator.
- **Teacher role (Moodle)** — Assigned by the loader ([P101](../platform/P101-moodle-user-role-enrolment.md)).
- **Course visible in its category** — After publishing, it sits in the category assigned by
  the reviewer.

## Notes

- Sub-state: `approved` (Ops ok) → `published` (loader confirmed, Moodle Link exists). The
  handoff is **not instantaneous** (loader is batch).
- The "instructor marker" lives in the intranet (own data: ≥1 approved course).
- The structure Google Doc is **no longer** a manual bridge: the `COURSE_MASTER_PLAN` is the
  loader's input, which builds the structure automatically ([PC](../platform/PC-loader-build-course.md)).

## Open questions

- **Dual handoff mechanism** — reconcile the GitHub Action (automatic, on-merge) from
  [Story 06](06-approval-github-pr.md) with the Sheet + batch loader described here. See
  [risks.md](../risks.md#dual-handoff-mechanism).
- **Post-publish edits / data consistency** — what happens when intranet data and Moodle
  content diverge after publication? See [risks.md](../risks.md#data-consistency).
