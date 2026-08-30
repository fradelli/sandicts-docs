---
title: Sandicts Dependency Security Remediation
doc-type: delivery-standard
role: source-of-truth
priority: high
canonical: docs/ai/dependency-security-remediation.md
related:
  - docs/ai/pull-request-standard.md
  - docs/ai/task-finalization-workflow.md
  - fradelli/reactjs-sandicts-web:docs/ai/ci-cd/security-audit-remediation.md
  - fradelli/nodejs-sandicts-api:docs/ai/ci-cd/security-audit-remediation.md
scope: frontend, backend, npm-audit, dependencies, security, jira, pull-requests
read-when:
  - responding to a dependency audit failure in any Sandicts application
  - remediating dependency vulnerabilities in frontend or backend
  - deciding how an unrelated pull request should handle a pre-existing audit failure
  - standardizing dependency-security delivery across repositories
do-not-read-when:
  - changing dependencies without a security or audit impact
  - changing only product or business documentation
---

# Sandicts Dependency Security Remediation

## Purpose

Keep dependency audit remediation consistent across the frontend and backend
without hiding risk, weakening CI, or mixing a pre-existing vulnerability into
an unrelated delivery.

This document owns the cross-repository policy. Each application repository
owns its exact validation commands, dependency paths, and runtime-specific
checks.

## Non-negotiable rules

- Keep `npm audit --audit-level=moderate` blocking in frontend and backend CI.
- Do not remove the audit job, lower its threshold, skip it, or convert a
  failure into a warning to make a pull request green.
- Do not run `npm audit fix --force` without explicit approval and a documented
  compatibility plan.
- Do not mix dependency remediation into an unrelated feature, fix, refactor,
  or documentation pull request.
- Use a dedicated Jira task and a separate remediation pull request for every
  affected application repository.
- Preserve a reviewable decision trail in Jira and every remediation pull
  request.

## Pre-existing failure isolation

When an unrelated pull request exposes an audit failure that already exists on
its base branch:

1. Confirm the pull request does not change `package.json` or the npm lockfile.
2. Reproduce the audit failure from the base branch or otherwise confirm from
   CI evidence that the vulnerability predates the pull request.
3. Create or reuse a dedicated security Jira task.
4. Create one remediation branch and pull request per affected application
   repository, based on that repository's integration branch.
5. Keep the unrelated pull request's dependency diff empty.
6. Merge the remediation pull request first.
7. Update the blocked branch from its integration branch and rerun CI.
8. Confirm the original pull request is green without carrying the remediation
   itself.

For Sandicts application repositories, use a branch such as
`chore/KAN-123-remediate-dependency-audit` and target `developer`. Shared policy
updates belong in a separate `sandicts-docs` pull request targeting `main`.

## Remediation workflow

For each affected application repository:

1. Run `npm ci` and reproduce the CI command
   `npm audit --audit-level=moderate`.
2. Inspect `npm audit --json` and use `npm explain <package>` to identify the
   vulnerable dependency paths.
3. Confirm patched versions with the npm registry or the owning project's
   official release information.
4. Select the smallest compatible remediation.
5. Recreate the dependency tree with `npm ci`.
6. Confirm the audit reports zero vulnerabilities at the configured threshold.
7. Run the complete repository validation matrix and any CLI or generated
   artifact check affected by the dependency change.
8. Record the evidence in the pull request and Jira task.

## Fix selection order

Use this order:

1. Upgrade a direct dependency inside the repository's supported major range.
2. Refresh the lockfile when an existing range already permits a patched
   transitive version.
3. Add a narrow override when a supported upstream package still pins a
   vulnerable transitive version.
4. Consider a breaking upgrade or forced audit fix only after explicit approval
   and a compatibility plan.

Do not accept an audit tool's proposed downgrade or broad major change without
checking it against the repository's architecture and supported runtime.

## Repository validation

Frontend validation is defined in
`fradelli/reactjs-sandicts-web:docs/ai/ci-cd/security-audit-remediation.md` and
must include clean install, audit, quality, tests, generated API contract, and
build.

Backend validation is defined in
`fradelli/nodejs-sandicts-api:docs/ai/ci-cd/security-audit-remediation.md` and
must include clean install, audit, lint, typecheck, tests, OpenAPI contract, and
build.

When an override affects a CLI or code generator, run that tool explicitly and
confirm it remains functional and creates no unrelated drift.

## Override requirements

An override must:

- target the narrowest dependency path npm supports
- select a published patched version compatible with the repository runtime
- identify the upstream package that still requires the override
- pass the affected tool or runtime checks
- be visible in the pull request and Jira decision record
- be removed when upstream ranges make it unnecessary

## Commit and pull request isolation

Use separate commits when process documentation changes with dependencies:

- `chore(security): ...` for `package.json`, lockfile, and dependency changes
- `docs(security): ...` for repository workflow documentation

Open separate pull requests for frontend, backend, and shared documentation.
Each pull request uses the common body from `docs/ai/pull-request-standard.md`
and describes only its own repository diff.

## Required delivery record

Record all of the following in Jira and the application pull request:

- vulnerable packages and severities
- whether each dependency is direct or transitive
- dependency paths that introduced the vulnerabilities
- direct upgrades, lockfile refreshes, and overrides selected
- rejected breaking or forced alternatives and why they were rejected
- clean-install and validation results
- residual risk, merge order, and any blocked pull request that must be updated

For a multi-repository remediation, the Jira task links every pull request and
states that the original blocked pull request can be updated only after the
relevant application remediation reaches its integration branch.
