---
id: "07"
title: Instructor promotion tools
track: product
status: draft
mvp: false
owner: Aria Cediel
depends_on: ["06"]
risk: low
jira: ENG-278
---

# Story 07 — Instructor promotion tools

**Tasks:** [implementation breakdown →](tasks/07-instructor-promotion-tools.tasks.md)

> Owner: [Aria Cediel](mailto:mkt@libreriadesatoshi.com). Distinct from
> [Story 06b](06b-marketing-publish-pipeline.md) (marketing's own posting): this story delivers
> a ready-to-share kit **to the instructor** so they can promote on their own channels.

## User story

**As** an approved instructor, **I want** to receive a ready-to-share social kit when my course
is published, **so that** I can promote it on my own channels and attract students.

## Proposed flow

1. **Base template:** the kit uses a pre-designed Canva template (horizontal format for
   X/LinkedIn + vertical format for IG Stories), with LdS branding.
2. **Trigger — `published` (Moodle Link exists):** an email is sent to the instructor with the
   complete kit, since at this point the real course link exists.
3. **Kit contents:**
   - Horizontal image (X/LinkedIn) — Canva link.
   - Vertical image (IG Stories) — Canva link.
   - 2–3 copy variants in Spanish, friendly tone, ready to copy/paste.
   - Direct link to the course on Moodle.

## Acceptance criteria

- On `published`, the instructor receives an email containing the kit (Canva links + copy +
  Moodle course link).
- The kit is generated from the LdS-branded Canva template (horizontal + vertical formats).
- The email is **independent** from the `approved` email (which confirms approval but doesn't
  yet have the Moodle link).

## Notes

- Since the `approved → published` handoff is not instantaneous (batch loader, per
  [Story 06](06-handoff-moodle-publication.md)), the instructor may receive this second email
  with some delay relative to approval — worth communicating so it doesn't cause confusion.
