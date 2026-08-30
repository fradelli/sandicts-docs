---
title: Sandicts Business Rules
doc-type: domain-rules
role: source-of-truth
priority: high
canonical: docs/business-rules/sandicts-business-rules.md
related:
  - docs/product/sandicts-product-context.md
  - docs/product/sandicts-mvp-scope.md
  - docs/product/sandicts-open-match-model.md
  - docs/product/sandicts-academy-plan-model.md
  - docs/product/sandicts-player-skill-allocation-model.md
  - docs/product/sandicts-v2-backlog.md
  - docs/decisions/shared-documentation-strategy.md
  - fradelli/nodejs-sandicts-api:docs/ai/api/error-handling-foundation.md
  - fradelli/nodejs-sandicts-api:docs/ai/api/zod-swagger-foundation.md
scope: business-rules, backend, marketplace, reservations, matches, payments, player-evolution
read-when:
  - implementing Sandicts domain modules
  - changing booking, payment, match, tournament, Organization, academy, or player behavior
  - designing API request and response contracts for domain flows
  - deciding which failures are business_rule_violation
do-not-read-when:
  - changing only visual frontend implementation
  - changing only CI, formatting, or logger internals
---

# Sandicts Business Rules

## Principle

Sandicts is a marketplace with two operational truths:

- organizations control court availability
- Sandicts controls the booking, match, payment, and tournament workflow exposed through the app

When backend behavior conflicts with this document, prefer this document unless the user explicitly changes the product direction.

## Core Entities

Recommended first-pass domain vocabulary:

- `User`: signed-in identity that can access one or more contexts
- `Player`: end user who books, joins matches, and enters tournaments
- `PlayerProfile`: player context attached to a user
- `Organization`: court, venue, arena, club, or organizer operator
- `OrganizationUnit`: physical location/branch controlled by an Organization
- `Court`: playable physical space controlled by an Organization or Organization unit
- `Academy`: training/classes/coaches/students context, independent from Organization
- `AcademyClass`: scheduled class/group lesson controlled by an academy
- `CoachAssignment`: permission that lets a coach manage assigned academy classes
- `AcademyPlan`: reusable commercial plan configured by an Academy
- `AcademyPlanAccess`: plan rules and access granted to an individual Player
- `AcademyPlanUsage`: auditable consumption or restoration of a plan use
- `Sport`: futevolei, beach tennis, beach volleyball, or similar
- `AvailabilitySlot`: time window made available by an Organization
- `Reservation`: booking attempt or confirmed booking for a court slot
- `OpenMatch`: scheduled game with joinable spots and an explicit lifecycle
- `OpenMatchParticipant`: registered Player participation and its lifecycle
- `OpenMatchGuest`: match-local participant managed by the creator without a
  Sandicts account
- `Tournament`: Organization-created competition or event
- `Payment`: system record of money status for reservations, Academy plan access, memberships, and tournaments
- `Membership`: recurring relationship between player and Organization or academy
- `Achievement`: optional future reward/status record for player participation, progression, or tournaments

Account context rules:

- Sandicts uses one login and one user identity.
- A user account can have Player, Organization, Academy, and Admin App
  contexts at the same time.
- The onboarding choice of Player, Organization, or Academy creates or opens the
  first context; it does not permanently define account type.
- Users with multiple contexts should switch through an app context selector.
- User-facing entity URLs should use slugs from the start while backend APIs can
  keep stable IDs internally.
- Public player profile URLs use `/players/:playerSlug` and must respect
  public, friends-only, or private visibility rules as those rules are
  implemented.

## Current MVP Decisions

MVP includes:

- player account access, including Google sign-in and Google One Tap
- basic player profile with main sport and simple level by sport
- Organization onboarding
- court registration and Organization-controlled availability
- court discovery by sport, availability, price, and Organization profile
- reservation request and confirmation
- Academy plan catalog and manual Player access/usage control
- basic manual payment status tracking
- player-created open matches

MVP excludes:

- geolocation and nearby-court search
- full player evolution and sport-specific skill allocation
- tournaments
- payment gateway integration, refunds, splits, payouts, and automated commission
- external student CRM, recurring memberships, coaches, classes, and full
  academy management
- full public player profile pages

For scope conflicts, prefer:

1. [`docs/product/sandicts-mvp-scope.md`](../product/sandicts-mvp-scope.md)
2. [`docs/product/sandicts-v2-backlog.md`](../product/sandicts-v2-backlog.md)
3. [`docs/product/sandicts-scope-checklist.md`](../product/sandicts-scope-checklist.md)

## Availability And Reservations

Rules:

- organizations are the source of truth for court availability
- a court can only be reserved in an available time slot
- a confirmed reservation must block the court slot
- the same court slot cannot have two active confirmed reservations
- a reservation should always reference Organization, court, sport, date, start time, end time, player, and status
- reservation status must be explicit

Recommended reservation statuses:

- `pending_payment`
- `confirmed`
- `canceled`
- `expired`
- `completed`

Business failures that should usually become `business_rule_violation`:

- attempting to reserve an unavailable slot
- attempting to reserve a slot already confirmed for another reservation
- attempting to modify or cancel a reservation after the allowed window
- attempting to confirm a reservation without a compatible payment state

## Open Matches

The detailed domain model and MVP/post-MVP boundary are defined in
`docs/product/sandicts-open-match-model.md`.

Rules:

- a player-created match must define intent, visibility, sport, date-time,
  capacity, place mode, and simple-level policy
- place mode is exactly one of a creator-controlled Sandicts reservation or a
  manual place that does not prove availability
- reservation-linked sport, court, and schedule are inherited and cannot drift
- only one active open match may reference the same reservation
- canceling or expiring the linked reservation before play cancels the match;
  canceling the match does not cancel the reservation
- an open match must expose total spots, confirmed participants, and remaining spots
- the creator is the first confirmed participant
- the creator cannot leave an active match; the MVP requires cancellation
  because it has no ownership-transfer flow
- only confirmed registered players and confirmed guests consume capacity
- a registered player cannot have duplicate active participation in a match
- public participation confirms immediately while capacity exists and may enter
  a waitlist when the match is full
- invite-only participation starts as a creator approval request
- a creator may manage a display-name-only guest without creating a `User` or
  `Player`
- a stable shared link may expose privacy-safe match data, but joining requires
  authentication and crawler requests cannot mutate participation
- match level should be treated as a filter and expectation, not as hard identity proof in the MVP
- essential participation, cancellation, reservation, reminder, and completion
  updates belong to the operational MVP; broad social notifications do not

Recommended open match statuses:

- `open`
- `full`
- `canceled`
- `completed`

Business failures:

- joining a full match when waitlist is disabled
- joining a canceled or completed match
- creating duplicate active participation
- confirming a participant or guest beyond capacity
- linking an incompatible or already-used reservation
- mutating reservation-derived court or schedule fields
- performing participation changes after the applicable cutoff

Accessing a missing reservation should become `resource_not_found`. Referencing
another player's reservation or performing creator-only actions should become
`forbidden`.

## Payments And Delinquency

Rules:

- payments must reflect their current status in the system
- reservation, Academy plan access, membership, and tournament flows should
  read payment status explicitly
- delinquency must be visible to organizations or academies only for their own
  students/customers
- organizations and academies must not see delinquency for unrelated contexts

Recommended payment statuses:

- `pending`
- `paid`
- `failed`
- `refunded`
- `overdue`

MVP note:

- payment provider integration comes after the MVP
- MVP payment status can be updated manually in controlled Organization,
  Academy, or Admin App flows
- keep the model ready for future provider references without coupling MVP behavior to a gateway
- use `refunded` only when a real refund or manual refund workflow exists
- Organization and academy billing must remain flexible until product chooses
  fixed subscription, commission/percentage, or a hybrid model

## Tournaments

MVP note:

- tournaments are V2, not MVP
- do not let tournament requirements block reservation, payment status, or open match delivery

Rules:

- organizations can create tournaments and events
- tournaments must have a participant limit
- users can register only while the tournament is open
- registration must respect participant limit
- tournament status must be explicit

Recommended tournament statuses:

- `open`
- `in_progress`
- `finalized`
- `canceled`

Business failures:

- registering after registration is closed
- registering when participant limit is reached
- registering twice
- changing results after finalization without an explicit administrative flow

## Organization Management

Rules:

- MVP organizations manage their own courts, schedules, reservations, and payment visibility
- academy students, memberships, coaches, classes, events, and richer financial
  management are V2 concerns unless scope changes
- Organization users must be scoped to their Organization account and assigned units/courts when applicable
- cross-Organization data leakage is a critical bug
- financial reports should start simple and auditable

MVP Organization visibility:

- pending and overdue payments
- agenda by day or week
- reservation status by court and time

V2 Organization reports:

- reservations by period
- revenue by period
- active students or members
- delinquency summaries

## Academy Management

Rules:

- Academy is independent from Organization.
- A user can own or work in one or more Academies and one or more Organizations
  under the same account.
- Academy owners/admins can create configurable plans with their own names,
  prices, calendar-day validity, total use allowance, and weekly use limit.
- Academy plan names are labels and must not determine validity or usage
  behavior.
- Selling or granting a plan creates immutable access rules for that Player;
  later plan edits affect only future access.
- Academy owners/admins can manually register and correct plan usage.
- Cross-academy access to plans, usage, or payment data is forbidden.
- Academy owners/admins can see and manage all academy classes.
- Coaches can view academy classes but manage only classes assigned to them.
- Coaches can accept students only for assigned classes and only when academy
  rules allow it.
- Cross-academy data leakage is a critical bug.
- Plan catalog and manual access/usage control are MVP.
- Full class, coach, student CRM, automatic attendance, and recurring billing
  management are V2.

MVP Academy plan visibility:

- active and inactive offers
- Player access and expiration state
- remaining total uses, when limited
- current seven-day window usage, when limited
- manual payment status

Detailed rules:

- [`docs/product/sandicts-academy-plan-model.md`](../product/sandicts-academy-plan-model.md)

V2 academy visibility:

- classes by day or week
- student requests
- coach assignments

## Player Evolution

MVP note:

- player evolution is V2, not MVP
- MVP only needs basic profile, main sport, and simple self-declared level by sport

V2 direction:

- each sport can define its own fundamentals, attributes, and special skills
- futevolei fundamentals may include chapa left, chapa right, shoulder, chest, head, thigh, attack head, and defense head
- futevolei attacks may include lobby, long diagonal, short diagonal, shark, voo da aguia, pingo, and pingo para tras
- plastic or high-skill moves should be modeled separately from base fundamentals
- each Sport and Level defines a skill point budget
- all Players at the same Sport and Level receive the same budget
- Players distribute the complete budget differently across eligible skills
- each skill allocation can range from 0 to 100
- the allocation sum validates the budget but must not be displayed as an
  overall or used as a cross-level ranking
- future sport profiles can show sport, photo, side, nationality, level, and
  skill distribution

Future questions:

- how scores are validated
- whether coaches, organizations, or match history can confirm progression
- how monthly evolution differs from permanent skill level
- how achievements, rankings, and AI suggestions relate to player evolution

Detailed rules:

- [`docs/product/sandicts-player-skill-allocation-model.md`](../product/sandicts-player-skill-allocation-model.md)

## API And Error Guidance

Rules:

- use Zod-backed DTOs for all external input and output
- use `AppError` for expected application/domain failures
- use `business_rule_violation` for valid requests that break domain rules
- keep validation failures as `validation_error`
- do not expose internal payment/provider details in public error responses

Examples:

- invalid date format: `validation_error`
- slot already taken: `business_rule_violation`
- player not found: `resource_not_found`
- Organization user accessing another Organization: `forbidden`
- coach managing an unassigned academy class: `forbidden`
- payment provider timeout: `internal_error` or provider-specific retry state, depending on the flow

## Web3 Boundary

Rules:

- tokens and NFTs are optional product layers
- core booking, payment, and tournament logic must work without blockchain
- achievements can be modeled internally first, then minted or mirrored later
- avoid storing irreversible blockchain assumptions in core reservation tables
