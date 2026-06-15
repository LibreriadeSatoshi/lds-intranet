# Roadmap — dependency graph, MVP cut & sequence

This is the execution view. The per-story files describe *what*; this describes *what
unblocks what* and *what ships first*.

## Dependency graph

```
PRODUCT TRACK
00 identity ─┬─▶ 01 entry ─▶ 02 profile ─▶ 03 submit (meta+content) ─▶ 05 approval ─┐
             │                                  └─▶ 04 drafts (cross-cut 02–03)      │
             │                                                                       ▼
             └─(auth for all)                                             ┌──▶ 06 HANDOFF ◀── 💥 critical
                                                                          │      │
PLATFORM TRACK (undefined today)                                          │      ├─▶ 06b mkt · 07 promo
   PA doc-gen ─▶ PB sequence ─▶ PC loader-build ───────────────────────────┘      └─▶ 08 profile
   P101 roles/enrolment ─────────────────────────────────────────────────┘
                                                                           09 help (parallel)
```

**Critical path:** `06 Handoff` is the only point that delivers real value (a live course in
Moodle) and it depends on the *entire* platform track (PA/PB/PC/P101) — all undefined. The
riskiest, least-specified work is on the critical path. **Spike the Moodle integration
first.**

## MVP cut — thin vertical slice (walking skeleton)

The thinnest end-to-end path that gets one instructor from entry to a live (hidden) course:

| Included (skeleton) | Deferred (post-skeleton) |
|---|---|
| 00 identity — **thin** (1 method, GitHub OIDC) | 00 — full Authentik 3-method federation |
| 01 entry · 02 profile · 03 submit (meta+content) | 04 — multi-draft + fine-grained autosave |
| 05 approval (PR merge) | 06b / 07 / 08 — marketing (Canva / Metricool) |
| 06 handoff (even if manual at first) | 09 — help / doc reference |
| PA / PC minimal (generate doc, loader uploads) | elegant batch handoff, featured, public profile polish |
| P101 (Teacher role assignment) | |

**Biggest cut candidate:** Story 00. A self-hosted Authentik IdP with 3 federated methods is
weeks of infra. For the pilot, a single method (GitHub OIDC — already needed for the PRs in
Story 05) delivers the same and collapses the "GitHub identity vs IdP identity" problem.
Authentik full = phase 2. **This is a business decision** — see
[risks.md](risks.md#identity-mvp-scope).

## Suggested sequence

1. **Spike (now):** Moodle integration — PA/PC/P101 feasibility against the real loader.
2. **Resolve one-way doors:** identity MVP scope + post-publish data-consistency model
   ([risks.md](risks.md)).
3. **Skeleton build:** 00(thin) → 01 → 02 → 03 → 05 → 06 with PA/PC/P101 minimal.
4. **Thicken:** 04 multi-draft, 06b/07/08 marketing, 09 help, Authentik federation.

## Status snapshot

| Track | draft | undefined/stub | ready |
|---|---|---|---|
| Product (00–09) | 00–08 (incl. 06b) | 09 (stub) | — |
| Platform (P101/PA/PB/PC) | — | all 4 | — |

Nothing is `ready` yet. Definition of the platform track is the gating work.
