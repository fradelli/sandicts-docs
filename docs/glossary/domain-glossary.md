---
title: Sandicts Domain Glossary
doc-type: domain-glossary
role: source-of-truth
priority: high
canonical: docs/glossary/domain-glossary.md
related:
  - docs/product/sandicts-product-context.md
  - docs/product/sandicts-mvp-scope.md
  - docs/product/sandicts-open-match-model.md
  - docs/product/sandicts-academy-plan-model.md
  - docs/product/sandicts-player-skill-allocation-model.md
  - docs/business-rules/sandicts-business-rules.md
scope: domain-vocabulary, entity-names, frontend-backend-alignment
read-when:
  - naming Sandicts entities, routes, screens, modules, DTOs, or Jira issues
  - replacing old Partner terminology
  - deciding whether a concept belongs to Organization, Academy, Player, or Admin App
do-not-read-when:
  - changing only low-level backend implementation details
  - changing only visual frontend styling
---

# Sandicts Domain Glossary

Use these names consistently in docs, Jira, backend, frontend, and AI routing.

## Canonical Entity Names

- `User`: signed-in identity that can access one or more app contexts.
- `Player`: end user who books courts, joins matches, and enters tournaments.
- `PlayerProfile`: player context attached to a user.
- `Organization`: court, venue, arena, club, or organizer operator.
- `OrganizationUnit`: physical location or branch controlled by an Organization.
- `Court`: playable physical space controlled by an Organization or Organization unit.
- `Academy`: training, classes, coaches, students, and plans context.
- `AcademyClass`: scheduled class or group lesson controlled by an academy.
- `CoachAssignment`: permission that lets a coach manage assigned academy classes.
- `AcademyPlan`: reusable commercial offer configured by an Academy.
- `AcademyPlanAccess`: individual plan access sold or granted to a Player,
  preserving the rules that applied at creation.
- `AcademyPlanUsage`: auditable consumption or restoration of one use from an
  AcademyPlanAccess.
- `Admin App`: internal Sandicts operational context for support, moderation, manual review, and controlled overrides.
- `Sport`: futevolei, beach tennis, beach volleyball, or similar sand sport.
- `AvailabilitySlot`: time window made available by an Organization for a court.
- `Reservation`: booking attempt or confirmed booking for a court slot.
- `OpenMatch`: scheduled game with joinable spots, explicit intent, visibility,
  place mode, capacity, and lifecycle.
- `OpenMatchParticipant`: registered Player participation and its lifecycle in
  one OpenMatch.
- `OpenMatchGuest`: match-local participant managed by the creator without a
  Sandicts account or public profile.
- `Tournament`: Organization-created competition or event.
- `Payment`: system record of money status for reservations, Academy plan
  access, memberships, and tournaments.
- `Membership`: recurring relationship between a player and an Organization or academy.
- `Achievement`: optional future reward/status record for player participation, progression, or tournaments.

## Naming Rules

The current authentication implementation names the product's signed-in
`User` identity `Account`. Existing API responses use `account`, and session
and profile relationships use `accountId`; this is not a second user entity.
Keep those technical names compatible until an explicit migration changes them.
For function names, commands, queries, and prototype identifiers, follow
[`Code Semantics`](../engineering/code-semantics.md).

- Do not use `Partner` as the current domain entity name.
- Use `Organization` for marketplace supply-side operators that expose courts,
  venues, arenas, clubs, or events.
- Use `Academy` for classes, coaches, students, training plans, and class
  management.
- Do not use `Membership` as a synonym for every Academy plan; a fixed class
  package may be non-recurring.
- Use `Admin App`, not only `Admin`, when referring to the internal Sandicts
  operational application/context.
- A single `User` can have Player, Organization, Academy, and Admin App
  contexts at the same time.
- The onboarding choice creates or opens the first context; it does not
  permanently define the account type.

## Route Direction

User-facing entity URLs should use slugs from the start while backend APIs can
keep stable IDs internally.

Recommended public/management route direction:

- players: `/players/:playerSlug`
- organizations: `/organizations/:organizationSlug`
- Organization management: `/organizations/:organizationSlug/manage`
- academies: `/academies/:academySlug`
- academy management: `/academies/:academySlug/manage`

Exact frontend route names can evolve in the frontend repository, but they
should preserve these entity names.
