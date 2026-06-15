# Risk & open-decision register

Live register. Each item is something that can hurt delivery or is a decision not yet made.
Linked from the stories that surface it.

## One-way doors (need a stakeholder decision before building)

### Identity MVP scope
**Risk:** Authentik self-hosted with 3 federated methods (local + Google + GitHub) is weeks of
infra for a pilot. **Decision needed:** GitHub-OIDC-only for the pilot (fast, cheap, already
required for [Story 05](stories/05-approval-github-pr.md) PRs) with Authentik federation in
phase 2 — *or* full federation as a hard requirement now.
**Input required:** volume of teachers/staff + timeline pressure.
Refs: [Story 00](stories/00-federated-identity.md), [roadmap](roadmap.md).

### Data consistency (Moodle ↔ intranet) {#data-consistency}
**Risk:** The intranet is source of truth for intake; Moodle owns post-handoff content. What
happens with **edits after publication** is undefined — it decides whether the handoff is
fire-and-forget or needs bidirectional reconciliation. This was the source document's own
opening "duda" and was never resolved.
**Decision needed:** are post-publish edits allowed, and on which side?
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
**Risk:** Two different handoff architectures are described as if they were one:
- [Story 05](stories/05-approval-github-pr.md): merge → **GitHub Action** → Moodle (automatic).
- [Story 06](stories/06-handoff-moodle-publication.md): `COURSE_MASTER_PLAN` → **Sheet** →
  **batch loader** → Moodle (not instantaneous).

**Action:** unify into one mechanism before building the handoff.

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
**Action:** define the GitHub ↔ IdP identity mapping. (Largely mitigated if the pilot uses
GitHub-OIDC-only — see *Identity MVP scope*.)

## Hygiene / smaller items

- **Numbering** — this folder follows the master's own numbering (0–9), preserving its two
  quirks: "Story 3" bundles metadata + content breakdown (kept in `03`), and the duplicated
  "Story 6" is split into `06` (handoff) and `06b` (marketing publish). Jira (`ENG-27x`) is an
  older off-by-one sequence, mapped per story in front-matter where possible.
- **Story 02 confirmation email** marked "Optional" — confirms nothing. Decide.
- **Story 05 companion `.yml` vs Markdown** for category/cohort/shortname — TBD.
- **Naming typo** — `COURSE_COURSE_MASTER_PLAN` in source; canonical is `COURSE_MASTER_PLAN`.
- **Language** — content captured as mixed ES/EN. Normalize to EN once the spec stabilizes
  (deferred to avoid churn on a moving doc).
- **Story 09** is a stub owned by Ivan; the **Story 06b / Story 08** Canva→Metricool
  automation is WIP.
