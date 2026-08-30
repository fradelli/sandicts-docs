---
title: Sandicts Docs Task Finalization Workflow
doc-type: delivery-workflow
role: source-of-truth
priority: high
canonical: docs/ai/task-finalization-workflow.md
related:
  - docs/ai/dependency-security-remediation.md
  - docs/ai/pull-request-standard.md
  - .codex/skills/jira-pr-commit-writer/SKILL.md
  - .github/pull_request_template.md
  - docs/product/sandicts-jira-planning-workflow.md
scope: git, github, jira, commits, pull-requests, validation, shared-docs
read-when:
  - finishing shared docs work with or without a Jira task
  - preparing commits for shared product, business-rule, glossary, or process docs
  - opening or updating a pull request in sandicts-docs
  - writing a PR title, PR description, commit message, or delivery summary
do-not-read-when:
  - changing only backend implementation
  - changing only frontend implementation
---

# Sandicts Docs Task Finalization Workflow

## Purpose

Define the standard workflow for finishing shared documentation work in
`fradelli/sandicts-docs`.

Use the same PR title and PR body structure as the frontend and backend
repositories. Repository-specific differences belong in validation details, not
in a different PR shape.

Use `docs/ai/pull-request-standard.md` as the shared source of truth for PR
title, body, validation, and no-blank-body rules.

## Pull Request Title Standard

Follow `docs/ai/pull-request-standard.md`.

When a Jira task exists, use the Jira key first:

```text
[KAN-123] <type>(<scope>): <short summary>
```

When the change is documentation-only and no Jira task exists, use:

```text
[NO-JIRA] docs(<scope>): <short summary>
```

Examples:

```text
[KAN-120] docs(process): standardize PR delivery format
[KAN-107] docs(planning): organize MVP docs and AI routing
[KAN-65] docs(product): document navigation context model
[NO-JIRA] docs(product): define academy plan rules
[NO-JIRA] docs(process): define documentation granularity
```

Rules:

- When a Jira task exists, its primary key must be the first characters in the
  PR title.
- `[NO-JIRA]` is allowed only in `fradelli/sandicts-docs`, when no Jira task
  exists and the PR contains documentation or documentation-routing metadata
  only.
- Do not use `[NO-JIRA]` for source code, runtime configuration, database
  schema, infrastructure, or implementation work.
- If a Jira task is created or discovered before merge, rename the PR to use
  the Jira key and update its Notes.
- Never open or leave a Sandicts PR titled with `[codex]`, only a branch name,
  or without an approved tracking prefix.
- If a publishing helper or GitHub UI proposes a different title, rewrite it to
  the Sandicts format before creating the PR.
- Use the Jira-backed title format in every Sandicts repository; the
  `[NO-JIRA]` exception is exclusive to `sandicts-docs`.
- If a PR includes multiple Jira tasks, put the primary key in the title and
  list related Jira keys in the PR body.

## Pull Request Description Standard

Follow `docs/ai/pull-request-standard.md` and always use
`.github/pull_request_template.md`.

Rules:

- Keep the template headings and order unchanged.
- Describe only the current PR changes, not the full parent epic.
- Include the primary Jira key and related Jira keys under `Notes`.
- For `[NO-JIRA]`, mark Jira fields as not applicable and record that the PR is
  documentation-only under `Tracking exception`.
- Mark validation checkboxes only for commands or checks that actually ran.
- Mention known gaps, skipped validations, or docs-only rationale explicitly.
- Keep the same PR body section structure across Sandicts repositories.
- Do not create or leave a PR with a blank body, omitted body, raw placeholders,
  or a shortened alternative body.

## Commit Message Standard

Use Conventional Commits:

```text
<type>(<scope>): <imperative summary>
```

Common shared docs examples:

```text
docs(process): standardize PR delivery format
docs(product): clarify MVP scope boundary
docs(glossary): align organization terminology
```

Rules:

- Use imperative mood.
- Keep the first line under 72 characters when practical.
- The PR title carries the Jira key by default or `[NO-JIRA]` for the approved
  documentation-only exception.
- Do not include a Jira key in commits unless the user explicitly asks for it
  or the commit will be consumed outside PR context.

## Validation Rule

For shared docs changes:

```bash
git diff --check
```

Also inspect the changed Markdown frontmatter, links, and skill routing metadata
when applicable.

Do not mark lint, typecheck, tests, build, or dependency audit as complete
unless this repository has those commands configured and they actually ran.

For frontend repository changes, follow
`fradelli/reactjs-sandicts-web:docs/ai/task-finalization-workflow.md`.

For backend repository changes, follow
`fradelli/nodejs-sandicts-api:docs/ai/task-finalization-workflow.md`.

For dependency audit remediation in frontend or backend, follow
`docs/ai/dependency-security-remediation.md`. Deliver one isolated application
PR per affected repository, merge remediation before updating any unrelated
blocked PR, and keep dependency changes out of shared documentation commits.

## Jira Status Rule

When the work has a Jira task, after the pull request is opened or updated:

1. add a Jira comment with PR link, changed scope, validation, and known gaps
2. move the delivered Jira issue to `In Review`
3. do not move the issue to `Concluido`; that happens only after review and
   merge are accepted

For `[NO-JIRA]` documentation-only work, skip Jira comments and transitions and
record `Jira status: not applicable` in the PR body.
