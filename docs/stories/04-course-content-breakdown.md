---
id: "04"
title: Course content breakdown (review-ready package)
track: product
status: draft
mvp: true
owner: TBD
depends_on: ["03"]
risk: medium
jira: TBD
---

# Story 04 — Course content breakdown (review-ready package)

> **Split note:** extracted from the source "Story 3". This is the part that produces a
> review-ready course package compatible with the GitHub review workflow
> ([Story 06](06-approval-github-pr.md)).

## User story

**As** a teacher, **I want** to break my course into classes with content and activities,
**so that** I produce a review-ready package as a Markdown template I can keep working on in
GitHub.

## Scope

**Covers:**
- Course content information.
- Generating a review-ready course package compatible with the GitHub review workflow
  ([Story 06](06-approval-github-pr.md)). Create a template (in a GitHub Markdown file) with
  the information gathered so far, so the teacher keeps working on the course.

**Does not cover:**
- The save process between steps (→ [Story 05](05-save-resume-drafts.md)).
- The approval process (→ [Story 06](06-approval-github-pr.md)).

## Required fields (per class)

- **Class Number / Title.**
- **Content:** Topics, Objectives.
- **Activities:** Quiz, Exercises.
- **Source Materials.**

## Generated Markdown template (course package shape)

```markdown
# Course Name
# What type of course would you like to offer?
- [ ] MOOC: Self-paced online course students can take anytime
- [ ] Cohort-Based Course: students progress together on a shared schedule
  | Field      | Information                       |
  |------------|-----------------------------------|
  | Start date |                                   |
  | End date   |                                   |
  | Classes    | How many classes your course has  |
  | Time       |                                   |
  | Time Zone  |                                   |
---
# Language
# 3 learning objectives / outcomes / competencies / skills
  1.
  2.
  3.
# Elevator pitch
  Describe the course in 1-2 sentences (30-second elevator test).
# What problem is this course solving for students, if any?
# Content Course
## Class Number N: Title of Class N
## Content class
### Topics
### Objectives
## Activities Class
- Quiz
- Exercise
- Etc...
## Source Materials
```

## Notes

- This Markdown is the artifact submitted as a Pull Request in
  [Story 06](06-approval-github-pr.md), and the basis for the `COURSE_MASTER_PLAN`
  consumed by the loader ([PA](../platform/PA-document-generation.md) /
  [PC](../platform/PC-loader-build-course.md)).
