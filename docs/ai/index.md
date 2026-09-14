---
title: Sandicts Shared AI Context Index
doc-type: ai-routing-index
role: routing-index
priority: high
canonical: docs/ai/index.md
scope: ai-routing, product, business-rules, shared-docs, frontend-backend-alignment
read-when:
  - starting shared Sandicts product or business-rule work
  - deciding which shared docs a backend or frontend task needs
  - finishing shared docs work or preparing a shared docs PR
  - auditing shared documentation for Codex token efficiency
do-not-read-when:
  - changing only backend architecture, tests, logging, config, or CI
  - changing only frontend components, styling, routing internals, or local state
---

# Sandicts Shared AI Context Index

Use this file as the shared documentation router. Read the smallest useful set
of files for the current task.

## Reading Rules

- Start here, then open only docs whose `read-when` entries match the task.
- Prefer concise source-of-truth docs before long working drafts.
- Treat `docs/product/sandicts-mvp-functional-spec.md` as on-demand detail, not
  default context.
- Keep backend implementation patterns in the backend repo.
- Keep frontend implementation, screen, and component rules in the frontend repo.
- Do not duplicate product rules in app repositories; link back here.

## Document Catalog

| Document | Role |
| --- | --- |
| [`docs/engineering/code-semantics.md`](../engineering/code-semantics.md) | Shared code naming criteria and implementation vocabulary |
| [`docs/product/sandicts-product-context.md`](../product/sandicts-product-context.md) | Product context and core marketplace direction |
| [`docs/product/sandicts-mvp-scope.md`](../product/sandicts-mvp-scope.md) | Approved MVP scope and explicit exclusions |
| [`docs/product/sandicts-open-match-model.md`](../product/sandicts-open-match-model.md) | Canonical MVP, V2, and future open-match domain model |
| [`docs/product/sandicts-open-match-implementation-roadmap.md`](../product/sandicts-open-match-implementation-roadmap.md) | Vertical delivery plan and read-only Jira alignment for Open Matches |
| [`docs/product/tweener-competitive-analysis.md`](../product/tweener-competitive-analysis.md) | Dated competitor evidence and product lessons behind the open-match model |
| [`docs/product/sandicts-academy-plan-model.md`](../product/sandicts-academy-plan-model.md) | MVP Academy plan configuration, access, usage, and expiration |
| [`docs/product/sandicts-player-skill-allocation-model.md`](../product/sandicts-player-skill-allocation-model.md) | V2 level budgets and sport-specific skill allocation without overall |
| [`docs/business-rules/sandicts-business-rules.md`](../business-rules/sandicts-business-rules.md) | Operational business rules shared by frontend and backend |
| [`docs/glossary/domain-glossary.md`](../glossary/domain-glossary.md) | Canonical entity names and domain vocabulary |
| [`docs/product/sandicts-v2-backlog.md`](../product/sandicts-v2-backlog.md) | V2 and future backlog boundaries |
| [`docs/product/sandicts-scope-checklist.md`](../product/sandicts-scope-checklist.md) | Working scope checklist; read only for scope classification |
| [`docs/product/sandicts-jira-planning-workflow.md`](../product/sandicts-jira-planning-workflow.md) | Jira roadmap and issue-writing workflow |
| [`docs/product/sandicts-mvp-functional-spec.md`](../product/sandicts-mvp-functional-spec.md) | Detailed MVP functional spec; read on demand |
| [`docs/decisions/shared-documentation-strategy.md`](../decisions/shared-documentation-strategy.md) | Ownership decision for shared vs app-specific docs |
| [`docs/decisions/api-contract-governance.md`](../decisions/api-contract-governance.md) | Cross-app API compatibility and delivery rules |
| [`docs/decisions/frontend-deployment-target.md`](../decisions/frontend-deployment-target.md) | Frontend provider, environment, CORS, cookie, and deployed authentication decision |
| [`docs/ai/dependency-security-remediation.md`](dependency-security-remediation.md) | Cross-app dependency audit remediation standard |
| [`docs/ai/pull-request-standard.md`](pull-request-standard.md) | Shared PR title, body, validation, and no-blank-body standard |
| [`docs/ai/task-finalization-workflow.md`](task-finalization-workflow.md) | Shared docs commit, PR, validation, and Jira review workflow |

## Common Reading Paths

For a product scope decision, read:

1. `docs/product/sandicts-product-context.md`
2. `docs/product/sandicts-mvp-scope.md`
3. `docs/business-rules/sandicts-business-rules.md`
4. `docs/product/sandicts-v2-backlog.md` only when classifying V2/future work

For a business-rule change, read:

1. `docs/business-rules/sandicts-business-rules.md`
2. `docs/product/sandicts-mvp-scope.md`
3. app-specific API or UI docs only after the shared rule is clear

For Academy plans, read:

1. `docs/product/sandicts-academy-plan-model.md`
2. `docs/product/sandicts-mvp-scope.md`
3. `docs/business-rules/sandicts-business-rules.md`

For open matches, read:

1. `docs/product/sandicts-open-match-model.md`
2. `docs/product/sandicts-mvp-scope.md`
3. `docs/business-rules/sandicts-business-rules.md`
4. `docs/product/sandicts-open-match-implementation-roadmap.md` when planning
   implementation or Jira work
5. `docs/product/tweener-competitive-analysis.md` only when the research basis
   or competitor comparison matters

For V2 player skill allocation, read:

1. `docs/product/sandicts-player-skill-allocation-model.md`
2. `docs/product/sandicts-v2-backlog.md`
3. `docs/business-rules/sandicts-business-rules.md`

For an API contract or compatibility change, read:

1. `docs/decisions/api-contract-governance.md`
2. `docs/decisions/shared-documentation-strategy.md`
3. the backend semantic API contract and generated OpenAPI
4. frontend API integration docs when client behavior changes

For frontend or browser-facing backend deployment work, read:

1. `docs/decisions/frontend-deployment-target.md`
2. the frontend deployment-environment document
3. the backend configuration foundation

For entity naming, read:

1. `docs/glossary/domain-glossary.md`
2. `docs/business-rules/sandicts-business-rules.md` only when behavior is also involved

For Jira planning, read:

1. `docs/product/sandicts-jira-planning-workflow.md`
2. `docs/product/sandicts-mvp-scope.md`
3. `docs/product/sandicts-v2-backlog.md` only for non-MVP boundaries

For finishing shared docs work, committing, or opening a PR, read:

1. `docs/ai/task-finalization-workflow.md`
2. `docs/ai/pull-request-standard.md`
3. `.codex/skills/jira-pr-commit-writer/SKILL.md`
4. `.github/pull_request_template.md`

For a dependency audit failure in frontend or backend, read:

1. `docs/ai/dependency-security-remediation.md`
2. the affected repository's `docs/ai/ci-cd/security-audit-remediation.md`
3. `docs/ai/pull-request-standard.md`
4. `docs/ai/task-finalization-workflow.md`

For frontend implementation, do not load all shared docs. Start in the frontend
repo and read shared docs only for product, scope, entity, or business-rule
questions.

For backend implementation, do not load all shared docs. Start in the backend
repo and read shared docs only for product, scope, entity, or business-rule
questions.
