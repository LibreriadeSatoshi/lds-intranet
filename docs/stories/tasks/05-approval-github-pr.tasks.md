---
story: "05"
title: Approval via GitHub PR — implementation tasks
status: draft
---

# Story 05 — Implementation tasks

> Tasks for [Story 05](../05-approval-github-pr.md). Owner: Ops.
> **Caveats:** the on-merge → Moodle upload depends on the loader/platform track
> ([Story 06](../06-handoff-moodle-publication.md), PC); and GitHub↔IdP identity must be
> reconciled — [risks.md](../../risks.md#github-vs-idp-identity),
> [#dual-handoff](../../risks.md#dual-handoff-mechanism).

## Repo & structure

- [ ] Create the single `courses` repo.
- [ ] Define the per-course folder/Markdown structure (matching [Story 03](../03-course-submission.md)).
- [ ] **Decide:** companion `.yml` vs in-Markdown for category / cohort / short name.

## Submission flow

- [ ] Document the fork/branch → PR submission path for instructors.
- [ ] GitHub auth for PRs (and consider GPG-signed commits as a nice-to-have).

## Review flow (Ops)

- [ ] Reviewer playbook: Approve / Request changes (comment required) / Close (comment required).
- [ ] Confirm push-to-PR re-opens review automatically; merge = sign-off.
- [ ] Rely on GitHub's built-in notifications for every review action.

## Publish automation

- [ ] GitHub Action on merge → upload course to Moodle in **'Hidden from Students'** state.
- [ ] Reconcile this automatic path with the batch-loader handoff ([Story 06](../06-handoff-moodle-publication.md)).

## Done when

- [ ] A merged PR is the sign-off and triggers the (single, agreed) Moodle upload mechanism.
