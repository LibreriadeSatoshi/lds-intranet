---
id: PB
title: Handoff temporal sequence
track: platform
status: undefined
mvp: true
owner: TBD
depends_on: ["PA"]
risk: high
jira: TBD
---

# PB — Handoff temporal sequence

> **Phantom story.** Referenced as MVP scope but never defined. See
> [risks.md](../risks.md#phantom-platform-stories).

## What is known (from references)

- On approval, document generation ([PA](PA-document-generation.md)) is triggered "following
  the temporal sequence (Story B)".
- The handoff is **not instantaneous** — the loader is batch
  ([Story 06](../stories/06-handoff-moodle-publication.md)).

## Open questions

- Define the full sequence: what happens, in what order, between PR merge and a confirmed
  Moodle Link. State transitions: `approved` → (generation) → (queued) → (loader run) →
  `published`.
- Reconcile with the **dual handoff mechanism** issue (GitHub Action vs Sheet + batch loader).
  See [risks.md](../risks.md#dual-handoff-mechanism).
