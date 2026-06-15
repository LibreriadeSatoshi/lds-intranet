---
id: PA
title: Document generation (MKT_BRIEFING / COURSE_MASTER_PLAN)
track: platform
status: undefined
mvp: true
owner: TBD
depends_on: ["03"]
risk: high
jira: TBD
---

# PA — Document generation (MKT_BRIEFING / COURSE_MASTER_PLAN)

> **Phantom story.** Referenced as MVP scope but never defined. See
> [risks.md](../risks.md#phantom-platform-stories).

## What is known (from references)

- On approval, the system generates two typed documents:
  - **`MKT_BRIEFING`** — for marketing (consumed by [Story 06b](../stories/06b-marketing-publish-pipeline.md)
    and [Story 08](../stories/08-public-instructor-profile.md)).
  - **`COURSE_MASTER_PLAN`** — for the instructor; the loader's input to build the course
    ([PC](PC-loader-build-course.md)).
- The content contract between intranet and Moodle is this **Doc**, not a YAML emitted by the
  intranet. The loader generates the YAML/spec internally from the `COURSE_MASTER_PLAN` using
  the **Claude API**.

## Open questions

- **Where is generation triggered?** The source mentions three places: the wizard ("we will
  generate a `.md`"), at approval (this story), and inside the loader (Claude API). Pin the
  pipeline. See [risks.md](../risks.md#artifact-generation-pipeline).
- Exact schema of `MKT_BRIEFING` and `COURSE_MASTER_PLAN`.
- Note: the source had a typo `COURSE_COURSE_MASTER_PLAN`; canonical name is
  `COURSE_MASTER_PLAN`.
