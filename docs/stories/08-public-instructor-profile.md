---
id: "08"
title: Public instructor profile, featured & marketing exports
track: product
status: draft
mvp: false
owner: Aria Cediel
depends_on: ["06", "PA"]
risk: low
jira: TBD
---

# Story 08 — Public instructor profile, featured & marketing exports

**Tasks:** [implementation breakdown →](tasks/08-public-instructor-profile.tasks.md)

> The master "Story 8" opens with a Canva → Metricool note (read approved `MKT_BRIEFING` →
> Canva asset → Metricool draft → MKT lead approves/posts) that overlaps with
> [Story 06b](06b-marketing-publish-pipeline.md); the pipeline mechanics live there. This story
> keeps the **profile / featured / role / export** acceptance criteria.

**img 2** _(pegar acá: ejemplo de change.org / compartir)_

## User story

**As** an approved instructor, **I want** a public profile and shareable course pages,
**and as** marketing **I want** access and exports, **so that** approved courses can be found
and promoted.

## Acceptance criteria

- **Public instructor profile** — A shareable public page listing my approved courses.
- **Social share** — Each approved course has share actions (copy link + ≥1 network).
- **Featured eligibility** — Admins flag courses as featured and surface them in a featured
  section.
- **Marketing role in the intranet** — A marketing role exists with access to courses for
  promotion purposes (fourth intranet role, alongside instructor, reviewer/Ops, and admin).
- **MKT_BRIEFING access** — Marketing consumes the generated `MKT_BRIEFING`
  ([PA](../platform/PA-document-generation.md)) as an intake input.
- **Course data export** — Marketing can export each course's data (wizard fields: title,
  description, audience, requirements, curriculum, bio, code) in a structured format to feed
  external tools.
- **Markdown export per course** — Each course exportable as a self-contained `.md`, a direct
  format for the team's content tools. Reuses data the intranet already owns; captures nothing
  new, just serializes.

## Notes

- No longer the custom-heavy story it was under the Moodle approach: public profile and
  featured are natural intranet features.
- "Course page" = intranet-native public landing (describes the course with wizard data) and
  links to the Moodle Link.
- "Entity with two views" pattern: internal view (dashboard) and public view (landing). Same
  data, two renders.
- "Featured" = a featured flag on the course model, unrelated to Moodle.
- The Canva → Metricool draft flow referenced at the top of the master story is specified in
  [Story 06b](06b-marketing-publish-pipeline.md).
