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
| 03 | [Course submission (metadata)](stories/03-course-submission.md) | draft | TBD |
| 04 | [Course content breakdown](stories/04-course-content-breakdown.md) | draft | TBD |
| 05 | [Save & resume drafts](stories/05-save-resume-drafts.md) | draft | TBD |
| 06 | [Approval via GitHub PR](stories/06-approval-github-pr.md) | draft | Ops |
| 07 | [Handoff to Moodle & publication](stories/07-handoff-moodle-publication.md) | draft | TBD |
| 08 | [Instructor promotion tools](stories/08-instructor-promotion-tools.md) | draft | Aria Cediel |
| 09 | [Marketing publish & social pipeline](stories/09-marketing-publish-pipeline.md) | draft | Aria Cediel |
| 10 | [Help / doc reference](stories/10-help-doc-reference.md) | stub | Ivan Fuentes |

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

The original document had a broken sequence (two "Story 6", an off-by-one against the
Jira labels, marketing stories with no number). This folder uses a **canonical 0–10**
numbering. The `ENG-27x` Jira tickets do not exist yet — they are a future way of working
and will inherit numbering from here, not the other way around.
