# Epic overview — Instructor onboarding & course submission

**Epic:** [ENG-271](https://b4os.atlassian.net/browse/ENG-271) (Jira label is aspirational; see [README](README.md#numbering-note))

Build an intranet app, independent of Moodle, that is the **source of truth** for the
course submission lifecycle: from a user deciding to teach, through a wizard, drafts, and
an approval workflow managed by Operations, to the handoff to Moodle. After handoff,
content lives in Moodle and the intranet keeps the record as history (basis for future
analytics). The intranet also hosts the public instructor profile and the course listing.

## The architectural inversion (the core decision)

The original proposal modeled course submission as a hidden Moodle course, relying on the
native "Course request" feature to collapse much of the flow into configuration.

That approach is **inverted**: the flow lives *outside* Moodle, in a parallel intranet app.
Moodle goes from being the container of the process to being the final destination of the
content.

```
   BEFORE (original)                     NOW (this spec)
   ┌──────────────────────┐       ┌─────────────┐      ┌─────────┐
   │       MOODLE         │       │   INTRANET  │ ───▶ │ MOODLE  │
   │  (contains the whole │  ==▶  │ (source of  │ hand │ (final  │
   │   hidden process)    │       │  truth)     │ off  │  dest.) │
   └──────────────────────┘       └─────────────┘      └─────────┘
```

## Architecture decisions (made — context)

- The intranet is the **source of truth** for intake and approval. Moodle = post-handoff execution.
- The content contract between intranet and Moodle is a **Doc** (`MKT_BRIEFING` /
  `COURSE_MASTER_PLAN`), **not** a YAML emitted by the intranet. The loader generates the
  YAML/spec internally from the `COURSE_MASTER_PLAN` using the **Claude API**.
- Post-approval, the intranet does not modify course content. Usage analytics = separate
  future flow, out of MVP scope.
- Identity must be transparent (see [Story 00](stories/00-federated-identity.md)).

## State of `moodle-course-loader` (actual repo, reviewed)

- Python CLI that creates courses in Moodle via the Web Services API.
- **Simple YAML path** (`CourseSpec`: `template_id`, `fullname`, `shortname`, `category_id`,
  `summary`, `visible`): only duplicates from a template (Phase 1).
- **Plan ₿ path** (`PlanBCourseBuilder` + `planb_source`): ALREADY builds complete structure
  in Moodle from a semi-structured markdown document (h1→Parts, h2→Chapters), creates
  sections and pages (`create_page`, `update_section`, `numsections`), uploads assets,
  renders HTML, embeds videos. **This machinery is reused for the `COURSE_MASTER_PLAN`.**
- Current client: `core_course_*` (duplicate, create, update, delete, get_contents,
  get_categories, get_courses_by_field), `upload_file`, `set_course_image`,
  `update_section`, `create_page`. **Does NOT yet have user/role/enrolment functions**
  (added by [P101](platform/P101-moodle-user-role-enrolment.md)).

## Related repositories

- `moodle-course-loader` — Python CLI that builds courses in Moodle via the Web Services
  API (consumes the `COURSE_MASTER_PLAN`).
- `courses` — GitHub repo where course submissions live as Pull Requests
  ([Story 05](stories/05-approval-github-pr.md)).

## MVP scope

Story 00 + Stories 01–06, plus platform stories P101 / PA / PB / PC. Marketing (06b, 07,
08) and help (09) are post-skeleton. See [roadmap.md](roadmap.md) for the proposed thin
vertical slice.

## Cross-cutting open question (from the source doc, unresolved)

> *"Cómo mantener la consistencia entre los datos de Moodle y los datos de la aplicación / wizard."*

The intranet is the source of truth for intake; Moodle owns post-handoff content. What
happens with edits after publication is **undefined** — it determines whether the handoff
is fire-and-forget or needs bidirectional reconciliation. Tracked in [risks.md](risks.md).
