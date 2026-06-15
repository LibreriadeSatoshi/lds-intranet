---
id: "06b"
title: Publish and course promotions (marketing)
track: product
status: draft
mvp: false
owner: Aria Cediel
depends_on: ["06", "PA"]
risk: medium
jira: TBD
---

# Story 06b — Publish and course promotions

**Tasks:** [implementation breakdown →](tasks/06b-marketing-publish-pipeline.tasks.md)

> **Numbering note:** the master labels this a second "Story 6" (distinct from the handoff
> story). Kept as `06b` to stay faithful to the master while avoiding a collision. Owner:
> [Aria Cediel](mailto:mkt@libreriadesatoshi.com).

## User story

**As** a marketer working at Librería, **I want** every approved course to generate a
ready-to-review social briefing immediately, and every published course to trigger the
scheduled posting of that content, **so that** promotion happens the same day the course goes
live without depending on the loader's timing.

## Acceptance criteria

1. **Trigger 1 — `approved`:** When the course status becomes `approved`, Aria receives a
   notification with the link to the `MKT_BRIEFING` (per [PA](../platform/PA-document-generation.md) /
   [PB](../platform/PB-handoff-sequence.md)).
2. **Draft creation:** Aria reviews the briefing and creates the post drafts in Metricool
   (X, LinkedIn) — copy, image, hashtags — without scheduling a date yet.
3. **Trigger 2 — `published`:** When the Moodle Link exists (course `published`, per
   [Story 06](06-handoff-moodle-publication.md)), the Nostr draft is automatically updated with
   the live course link and scheduled for same-day posting.
4. **Nostr exception:** Since Metricool doesn't support Nostr natively, this channel is posted
   manually by Aria; the briefing should flag it as a pending manual step.
5. **Status tracking:** The Sheet/intranet reflects "promo ready" after step 2 and
   "promo published" after step 3.

## Notes

- Decouples creative work (done at `approved`, with no time pressure) from the technical
  publish event (unpredictable due to the batch loader).
- See [Story 08](08-public-instructor-profile.md) for the marketing role, MKT_BRIEFING access,
  and the Canva → Metricool tooling that overlaps with this pipeline.

## Open questions

- **Posting cadence** if multiple courses are approved/published the same day (proposal: max 1
  new-course post/day, FIFO queue) — needs decision.
- The Canva → Metricool automation (asset generation → draft) needs to be specified end to
  end; see [Story 08](08-public-instructor-profile.md) and [risks.md](../risks.md).
