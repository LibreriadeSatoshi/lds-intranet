---
id: P101
title: Moodle user / role / enrolment functions
track: platform
status: undefined
mvp: true
owner: TBD
depends_on: []
risk: high
jira: TBD
---

# P101 — Moodle user / role / enrolment functions (loader)

> **Phantom story.** Referenced as MVP scope throughout the spec but never defined in the
> source document. This file captures what is known so the gap is explicit. See
> [risks.md](../risks.md#phantom-platform-stories).

## What is known (from references)

- The `moodle-course-loader` currently has `core_course_*`, `upload_file`,
  `set_course_image`, `update_section`, `create_page` — but **does NOT yet have
  user/role/enrolment functions**.
- This story adds those: assigning the **Teacher / editing teacher** role to the instructor
  in Moodle on publish.
- Auth for these Web Service calls uses `wstoken` (separate plane from the OIDC login token,
  per [Story 00](../stories/00-federated-identity.md)).

## Why it matters

[Story 06](../stories/06-handoff-moodle-publication.md) ("Teacher role assigned by the
loader") cannot complete without this. It is on the critical path.

## Open questions

- Full definition pending: which Web Service functions, role assignment by course context,
  enrolment of the instructor, idempotency on re-run.
