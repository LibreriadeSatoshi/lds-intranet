---
id: "09"
title: Marketing publish & social pipeline
track: product
status: draft
mvp: false
owner: Aria Cediel
depends_on: ["07", "PA"]
risk: medium
jira: TBD
---

# Story 09 — Marketing publish & social pipeline

> **Fusion note:** merges the source's duplicate "Story 6 — Publish and course promotions"
> and "Story 8" (untitled). Both are owned by Aria / Marketing and describe the same
> GitHub → Canva → Metricool pipeline. Heavily WIP in the source.

Owner: [Aria Cediel](mailto:mkt@libreriadesatoshi.com)

## User story

**As** a marketer working at Librería, **I want** to promote all courses — as they are
approved — on our social media, **so that** approved courses get published the same day, ASAP.

## Proposed pipeline

1. **GitHub** — read the approved `MKT_BRIEFING`.
2. **Canva API** — create a digital asset.
3. **Read / download** the asset.
4. **Metricool** — feed the asset + information as a draft.
5. **Approve & post** — Aria / MKT lead approves and posts.

Target channels: X, LinkedIn (LN), Instagram (IN), Nostr.

## Acceptance criteria

- Marketing can access the approved `MKT_BRIEFING` ([PA](../platform/PA-document-generation.md)).
- An asset is generated (Canva) from the briefing.
- The asset + course info land in Metricool as a draft.
- A marketing approver can publish to the configured channels.

## Open questions

- The source steps were placeholders (`1. S / 2. Df / 3. D / 4. D / 5.`) and an explicit
  *"\_\_something happens magically\_\_"* between reading the briefing and Metricool. The
  Canva → Metricool automation needs to be specified end to end.
- Same-day / ASAP SLA — define what triggers it (on merge? on publish confirmation?).
