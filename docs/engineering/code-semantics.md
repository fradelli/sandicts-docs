---
title: Sandicts Code Semantics
doc-type: engineering-guideline
role: source-of-truth
priority: high
canonical: docs/engineering/code-semantics.md
related:
  - docs/glossary/domain-glossary.md
  - docs/business-rules/sandicts-business-rules.md
scope: naming, code-organization, backend, frontend, codex
read-when:
  - adding or renaming functions, files, modules, hooks, or domain contracts
  - implementing business commands or filtered domain queries
  - integrating prototypes with the API
do-not-read-when:
  - changing only visual styling or dependency versions
---

# Sandicts Code Semantics

Names should let a caller predict the operation, its business scope, and its
important effects without reading the implementation. Use the domain glossary
for entities and the actual API contract for integration fields.

## Commands and queries

- Name business commands by action and target: `confirmReservation`,
  `cancelOpenMatch`, `recordManualPayment`. These illustrate future operations,
  not currently implemented capabilities.
- Reveal creation, linking, consumption, or revocation when a less explicit
  verb would suggest a read. `resolveOrCreateAccountForGoogleSignIn` can create
  an account and link its Google identity; it is not a lookup.
- A replacement that revokes old records and creates a new one should make
  those effects explicit, such as `revokeActiveChallengesAndCreate`. Document
  atomicity in the port, not only in one persistence adapter.
- Queries should reveal meaningful restrictions: `findActiveSportById` can
  return nothing for an existing inactive sport. `searchActiveSportsByName`
  searches names among active sports; its results follow catalog order.
- Reserve persistence verbs for storage with the stated lifetime.
  `applyAuthSessionSnapshot` updates runtime memory and query cache;
  `resetInitialAuthSessionPromise` resets initialization bookkeeping only.
- Do not list every implementation step in the name. Put detailed guarantees
  in the contract when the action and target already communicate intent.

## UI and reusable helpers

- Event callbacks may use `handle...` near the event. When a callback implements
  a meaningful operation, expose that intent: `changeMainSportAndResetLevel`
  resets the level only when the selected sport changes.
- Simulated operations should retain a simulation name when they can be
  mistaken for real validation or API work. Do not copy prototype callbacks
  into production while preserving simulated success behavior.
- Technical helpers and generic components may remain generic. `cn`, time
  conversion, mappers, `Button`, and `PageState` do not need business entity
  names. A helper imported twice is not automatically a shared domain concept.
- `execute` is appropriate on a semantically named use case. Avoid repeating
  the whole class name in every method.

## Files and directories

Use a responsibility name and the repository's existing suffix conventions.
`apply-auth-session-snapshot.ts` identifies a coordinator with one operation.
Local `.helpers.ts` or `.utils.ts` files are acceptable when their functions
have one cohesive subject. Split unrelated responsibilities as they grow;
do not create a file per function solely to satisfy a naming rule.

Keep framework names such as `page.tsx`, `layout.tsx`, and export entrypoints.
Never rename generated Prisma or Orval files manually. Contract changes require
the existing compatibility workflow, not a cosmetic naming sweep.

## Current vocabulary and integration boundaries

`User` names the signed-in identity in product documentation. The current auth
implementation represents that identity as `Account`, with `accountId` in
session/profile relationships and `account` in API responses. These are the
product and implementation names for the current identity; this distinction
does not introduce a second user entity. Keep existing technical names until
an explicit migration requires changing them.

The current API uses `mainSportCode` and `mainSportLevel`. The onboarding
prototype has separate scenario-local `mainSportId` and `mainSportLevelId`,
plus level-scale fixtures. Do not pass those IDs directly to the API or assume
prototype levels are accepted values. Integration must map selected options
to the generated contract's codes and supported levels, or align the prototype
model deliberately. Do not activate extra sports or level policies from fixtures.

## Review before finishing an implementation

For each new or changed public helper, command, hook, or file:

1. Check whether its name promises a read while its implementation writes,
   creates, links, revokes, consumes, or clears related state.
2. Check whether omitted filters, lifecycle states, or identifiers change what
   the caller should expect.
3. Check domain vocabulary and distinguish IDs, codes, slugs, and UI fixtures.
4. Keep generic infrastructure generic and domain-specific behavior near its
   feature. Avoid unrelated renaming during a bounded change.
5. Update callers, mocks, existing tests, imports, and affected docs together;
   run typecheck and tests for the changed flows.

This is a semantic review criterion, not a forbidden-word regex. A linter
cannot establish whether a name accurately expresses a business rule.
