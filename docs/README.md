# Instructor onboarding & course submission — Product spec

Source of truth for the **ENG-271** epic (intranet-first). This folder replaces the
single consolidated working document; each story now lives in its own file.

> **Source:** consolidated working document, 2026-06-09. Split into per-story files
> on 2026-06-15. Content captured faithfully (mixed ES/EN); language normalization
> to EN is deferred — see [risks.md](risks.md).

## How to read this

1. Start with **[overview.md](overview.md)** — the architectural inversion (Moodle → intranet),
   the decisions already made, and the real state of the `moodle-course-loader`.
2. **[roadmap.md](roadmap.md)** — dependency graph, MVP cut, and delivery sequence.
   This is what gets executed.
3. **[risks.md](risks.md)** — live register of open risks and unresolved decisions.
4. Then the stories below.

## Status legend

`draft` raw capture · `refined` reviewed & coherent · `ready` implementable

## Product stories (`stories/`)

| # | Story | Status | Owner |
|---|---|---|---|
| 00 | [Federated identity via dedicated IdP](stories/00-federated-identity.md) | draft | TBD |
| 01 | ["Teach on LdS" entry point](stories/01-entry-point.md) | draft | TBD |
| 02 | [Teacher profile wizard](stories/02-teacher-profile-wizard.md) | draft | TBD |
| 03 | [Course submission (metadata + content breakdown)](stories/03-course-submission.md) | draft | TBD |
| 04 | [Save & resume drafts](stories/04-save-resume-drafts.md) | draft | TBD |
| 05 | [Approval via GitHub PR](stories/05-approval-github-pr.md) | draft | Ops |
| 06 | [Handoff to Moodle & publication](stories/06-handoff-moodle-publication.md) | draft | TBD |
| 06b | [Publish and course promotions (marketing)](stories/06b-marketing-publish-pipeline.md) | draft | Aria Cediel |
| 07 | [Instructor promotion tools](stories/07-instructor-promotion-tools.md) | draft | Aria Cediel |
| 08 | [Public instructor profile, featured & exports](stories/08-public-instructor-profile.md) | draft | Aria Cediel |
| 09 | [Doc reference / teacher help](stories/09-doc-reference.md) | stub | Ivan Fuentes |

Implementation task breakdowns per story live in
[`stories/tasks/`](stories/tasks/README.md) (00–08).

## Platform / loader stories (`platform/`)

These are referenced as MVP scope throughout the spec but were **never defined** in the
source document. They are the Moodle-integration core. See [risks.md](risks.md#phantom-platform-stories).

| # | Story | Status |
|---|---|---|
| P101 | [Moodle user / role / enrolment functions](platform/P101-moodle-user-role-enrolment.md) | undefined |
| PA | [Document generation (MKT_BRIEFING / COURSE_MASTER_PLAN)](platform/PA-document-generation.md) | undefined |
| PB | [Handoff temporal sequence](platform/PB-handoff-sequence.md) | undefined |
| PC | [Loader builds course in Moodle](platform/PC-loader-build-course.md) | undefined |

## Numbering note

This folder **follows the master document's own numbering (0–9)**. Two quirks are preserved
faithfully:

- The master's "Story 3" bundles course **metadata** and the per-class **content breakdown**
  under one number — kept together in [03](stories/03-course-submission.md) (Parts A and B).
- The master has **two "Story 6"**: the handoff ([06](stories/06-handoff-moodle-publication.md))
  and the marketing publish/promotions pipeline, kept as
  [06b](stories/06b-marketing-publish-pipeline.md) to avoid a collision.

The `ENG-27x` Jira labels in the master are an older, off-by-one sequence; where a clean
mapping exists it is recorded in each story's front-matter `jira` field.
