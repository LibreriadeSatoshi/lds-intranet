# Risk & open-decision register

Live register. Each item is something that can hurt delivery or is a decision not yet made.
Linked from the stories that surface it.

## One-way doors (need a stakeholder decision before building)

### Identity MVP scope
**RESOLVED (PRD, decision-log Q1):** full **3-method federation in v1** (local + Google +
GitHub via self-hosted Authentik), 2 devs dedicated. The GitHub-OIDC-only pilot path is **not**
taken.
**Original risk (for the record):** Authentik with 3 federated methods is weeks of infra; the
alternative was a GitHub-OIDC-only pilot with federation in phase 2.
Refs: [Story 00](stories/00-federated-identity.md), [roadmap](roadmap.md).

### Data consistency (Moodle ↔ intranet) {#data-consistency}
**RESOLVED (PRD OQ-2):** fire-and-forget. After publish, content lives **only in Moodle**
(edits there); the intranet keeps the historical record only — no bidirectional reconciliation.
**Original risk (for the record):** what happens with edits after publication was undefined
(the source document's opening "duda").
Refs: [overview](overview.md), [Story 06](stories/06-handoff-moodle-publication.md).

## Engineering risks (resolvable by the team, but high impact)

### Phantom platform stories {#phantom-platform-stories}
**Risk:** P101 / PA / PB / PC are declared MVP scope but were never defined in the source.
They are the Moodle-integration core and sit on the critical path to
[Story 06](stories/06-handoff-moodle-publication.md). If undefined → no value delivered.
**Action:** spike + define before committing dates.
Refs: [P101](platform/P101-moodle-user-role-enrolment.md),
[PA](platform/PA-document-generation.md), [PB](platform/PB-handoff-sequence.md),
[PC](platform/PC-loader-build-course.md).

### Dual handoff mechanism {#dual-handoff-mechanism}
**RESOLVED (PRD OQ-1):** the PR **merge triggers the build automatically** (the build itself
may run async/queued); there is **no manual Sheet step**. The intranet records the handoff event
and notifies Operations on merge.
**Original risk (for the record):** two architectures were described as one — Story 05's
merge→GitHub-Action→Moodle vs Story 06's `COURSE_MASTER_PLAN`→Sheet→batch-loader.
**Still for architecture:** detailed sequence/state machine, queue vs sync, error/retry,
idempotency (PRD FR-36, OQ-6 spike).

### Artifact generation pipeline {#artifact-generation-pipeline}
**Risk:** Three places claim to generate the course doc: the wizard
([Story 03](stories/03-course-submission.md) "we generate a `.md`"), at approval
([PA](platform/PA-document-generation.md)), and inside the loader (Claude API). The pipeline
is muddy.
**Action:** pin a single source of generation for each artifact.

### GitHub vs IdP identity {#github-vs-idp-identity}
**Risk:** PR authorship/review ([Story 05](stories/05-approval-github-pr.md)) is GitHub-based;
the rest of the system keys on the IdP `sub` ([Story 00](stories/00-federated-identity.md)).
The mapping between the two identities is unaddressed. GPG-signed commits sharpen it.
**Action:** define the GitHub ↔ IdP identity mapping. **Still open — build-blocking (PRD OQ-4).**
(The earlier "mitigated by a GitHub-OIDC-only pilot" escape no longer applies: full 3-method
federation was chosen — see *Identity MVP scope* — so this mapping must be solved.)

## Hygiene / smaller items

- **Numbering** — this folder follows the master's own numbering (0–9), preserving its two
  quirks: "Story 3" bundles metadata + content breakdown (kept in `03`), and the duplicated
  "Story 6" is split into `06` (handoff) and `06b` (marketing publish). Jira (`ENG-27x`) is an
  older off-by-one sequence, mapped per story in front-matter where possible.
- **Story 02 confirmation email** — RESOLVED (PRD OQ-7): **required** (was marked "Optional").
- **Story 05 companion `.yml` vs Markdown** for category/cohort/shortname — TBD (PRD OQ-5, → architecture).
- **Naming typo** — `COURSE_COURSE_MASTER_PLAN` in source; canonical is `COURSE_MASTER_PLAN`.
- **Language** — content captured as mixed ES/EN. Normalize to EN once the spec stabilizes
  (deferred to avoid churn on a moving doc).
- **Story 09** is a stub owned by Ivan; the **Story 06b / Story 08** Canva→Metricool
  automation is WIP.
