---
id: PC
title: Loader builds course in Moodle
track: platform
status: undefined
mvp: true
owner: TBD
depends_on: ["PA", "P101"]
risk: high
jira: TBD
---

# PC — Loader builds course in Moodle

> **Phantom story.** Referenced as MVP scope but never defined. See
> [risks.md](../risks.md#phantom-platform-stories).

## What is known (from references)

- When the `COURSE_MASTER_PLAN` is ready, its reference enters the Sheet the loader
  processes; the loader builds the course
  ([Story 06](../stories/06-handoff-moodle-publication.md)).
- The **Plan ₿ machinery already exists** in `moodle-course-loader`: `PlanBCourseBuilder` +
  `planb_source` builds complete structure from a semi-structured markdown document
  (h1→Parts, h2→Chapters), creates sections and pages, uploads assets, renders HTML, embeds
  videos. **This is reused for the `COURSE_MASTER_PLAN`** — so this story is largely
  adaptation, not greenfield.
- Course is created in the **'Hidden from Students'** state on publish.

## Open questions

- Map the `COURSE_MASTER_PLAN` schema onto the existing `PlanBCourseBuilder` input.
- Write-back of the Moodle Link to the intranet (confirmation step in
  [Story 06](../stories/06-handoff-moodle-publication.md)).
- Idempotency / re-run behavior.
