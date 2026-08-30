---
title: Sandicts Open Match Implementation Roadmap
doc-type: delivery-roadmap
role: working-plan
priority: high
canonical: docs/product/sandicts-open-match-implementation-roadmap.md
related:
  - docs/product/sandicts-open-match-model.md
  - docs/product/sandicts-mvp-functional-spec.md
  - docs/product/sandicts-jira-planning-workflow.md
  - docs/business-rules/sandicts-business-rules.md
scope: product, delivery, roadmap, open-matches, backend, frontend, e2e, jira
read-when:
  - planning or implementing the Open Matches epic
  - splitting open-match work into Jira issues
  - sequencing backend, frontend, and E2E delivery
do-not-read-when:
  - deciding product rules without reading the open-match model first
  - implementing unrelated MVP modules
---

# Sandicts Open Match Implementation Roadmap

## Purpose

This plan translates `docs/product/sandicts-open-match-model.md` into vertical,
testable delivery increments. It does not replace the domain rules.

Jira audit date: 2026-08-08.

The initial audit was read-only. After explicit approval on 2026-08-08, the
existing Epic and share task were aligned and the missing child Stories were
created.

## Current Jira Alignment

Current items:

- `KAN-54` - `[MVP] Open Matches` Epic, updated with the canonical scope,
  success criteria, delivery increments, exclusions, and open decisions
- `KAN-151` - `[Open Matches] Build core match model and creation/discovery
  flow`, required Story
- `KAN-152` - `[Open Matches] Implement participation, approval, guests, and
  waitlist`, required Story
- `KAN-127` - `[Open Matches] Add shareable match links and social previews`,
  retained and aligned Task
- `KAN-153` - `[Open Matches] Synchronize reservations and operational
  updates`, required Story
- `KAN-154` - `[Open Matches] Complete matches and enable rebook or rematch`,
  required Story
- `KAN-155` - `[Open Matches] Suggest compatible players with deterministic
  rules`, optional Low-priority Story after core validation

Delivery dependency links:

- `KAN-151` blocks `KAN-152`
- `KAN-152` blocks `KAN-127` and `KAN-153`
- `KAN-127` and `KAN-153` block `KAN-154`
- `KAN-154` blocks optional `KAN-155`

All children remain in `A fazer`. Required Stories and `KAN-127` have Medium
priority; optional `KAN-155` has Low priority.

Each item is a vertical outcome with backend contract, frontend states, and
integrated validation. Add backend/frontend/E2E subtasks only when separate
ownership or scheduling makes them useful.

## Decision Gate

Resolve these before implementation starts:

- leave, removal, and cancellation cutoffs
- no-show authority and dispute policy
- always-on versus creator-configurable waitlist
- public versus unlisted indexation and approved preview fields
- operational update channels and timing
- completion policy

The following may be resolved before their later increment instead of blocking
core development:

- participant cost responsibility and future payment split presentation
- discoverability opt-in for deterministic suggestions

## Increment 1: Core Match And Discovery

Outcome:

- creator can publish a correctly classified match and players can discover or
  read it

Backend:

- model `OpenMatch` intent, visibility, level policy, lifecycle, capacity, and
  place mode
- support reservation reference and manual place
- validate creator ownership and reservation compatibility
- enforce one active match per reservation
- expose create, list, and detail contracts
- filter by sport, intent, simple level, and status where accepted

Frontend:

- build list, filters, detail, and stepwise creation
- distinguish reservation-backed from manual-place matches
- show capacity, visibility, intent, simple-level policy, and reservation state
- render open, full, canceled, and completed states

Integrated gate:

- creator creates one reservation-backed and one manual-place match
- derived reservation fields cannot drift
- incompatible, foreign, or already-used reservation fails correctly
- public discovery excludes `invite_only` matches

## Increment 2: Participation, Approval, Guests, And Waitlist

Outcome:

- organizer can fill capacity with registered players and off-platform guests
  while every participant has an understandable state

Backend:

- model registered participation and guest records separately
- make creator the first confirmed participant
- implement public confirmation and invite-only request/approve/decline
- enforce uniqueness and capacity atomically
- implement leave, creator removal, waitlist, and promotion policy
- preserve participant history across terminal states

Frontend:

- show confirmed, requested, waitlisted, declined, left, and no-show states
- give creator request and guest management controls
- show the current player's state and available action
- explain capacity and cutoff failures

Integrated gate:

- concurrent joins cannot overfill the match
- duplicate active participation is blocked
- invite-only request consumes capacity only after confirmation
- guest occupies capacity without creating an account
- released capacity reopens the match and promotes the queue according to policy

## Increment 3: Shared Link And Acquisition

Outcome:

- a creator can distribute a match in WhatsApp and a visitor can safely return
  from authentication to participation

Delivery issue:

- use existing `KAN-127`; do not create a replacement

Integrated gate:

- stable link supports native share, copy, and WhatsApp intent
- social preview is server-rendered and privacy-safe
- crawler and visitor reads have no side effects
- authentication preserves the intended match
- full, canceled, completed, unavailable, and not-found states block invalid
  participation

## Increment 4: Reservation Synchronization And Operational Updates

Outcome:

- real-world reservation or participant changes cannot silently leave a stale
  match

Backend:

- propagate linked reservation confirmation, expiration, and cancellation
- cancel the match when its reservation becomes unusable before play
- publish domain events for participation and match transitions
- schedule the accepted pre-match reminder

Frontend:

- surface pending reservation/payment, cancellation cause, and reopened capacity
- display essential update delivery state only where operationally useful

Integrated gate:

- reservation expiration/cancellation updates the match once and idempotently
- canceling only the match leaves the reservation unchanged
- participant promotion, decline, removal, and match cancellation trigger the
  accepted operational update

## Increment 5: Completion, Return Loop, And Metrics

Outcome:

- completed play creates a measurable path back to a reservation or similar
  match

Backend:

- apply the accepted completion policy
- preserve final participant state and allowed no-show record
- expose rematch/rebook source data
- record fill, time-to-fill, link conversion, cancellation, no-show, waitlist,
  guest, and repeat-play events

Frontend:

- show lightweight completion confirmation
- show final participant state
- offer rebook when a reservation exists and create-similar/rematch in all valid
  cases

Integrated gate:

- completed match rejects new participation
- creator can follow the accepted completion flow
- rebook/rematch prefill only approved fields
- MVP metric events contain no private contact data

## Increment 6: Deterministic Suggestions

This increment is part of MVP validation but is not a launch gate for the first
controlled release.

Outcome:

- creator receives an explainable candidate list without AI or automatic
  messaging

Rules:

- require discoverability opt-in
- match sport and compatible simple level
- do not use precise live location
- do not reveal contact data, auto-enroll, or auto-message
- make sharing the primary action when the candidate pool is empty

## Cross-Cutting Requirements

Every increment must cover:

- authenticated authority and cross-context access
- validation and stable error semantics
- idempotent transitions where retries are possible
- accessible loading, empty, success, blocked, and terminal states
- responsive behavior
- OpenAPI contract compatibility between backend and frontend
- focused unit/integration coverage plus the increment's critical E2E path
- privacy-safe logs, analytics, public pages, and update payloads

## Recommended Delivery Order

Dependencies:

`KAN-151 -> KAN-152 -> {KAN-127, KAN-153} -> KAN-154 -> KAN-155 (optional)`

Increment 4 event contracts can be designed in parallel with Increment 2 after
the core lifecycle is stable. Increment 6 starts only after the core metrics can
show whether match filling is a real problem.

## Release Gate

The controlled MVP release is ready when Increments 1 through 5 pass their
integrated gates and the launch-blocking decisions are resolved.

Do not delay the controlled release for:

- deterministic suggestions
- groups or directed invitations
- guest claiming
- ratings, reputation, rankings, or achievements
- chat or feed
- automatic matchmaking or AI

## Roadmap Alignment Needed At Implementation Start

The active frontend roadmap currently describes Open Matches as a single phase
for create/list/detail/join/leave/cancel. When implementation is authorized,
update it to reference this roadmap and mirror the six increments instead of
copying the domain rules into the frontend repository.
