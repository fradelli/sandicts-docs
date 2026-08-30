---
title: API Contract Governance
doc-type: cross-app-architecture-decision
role: source-of-truth
priority: high
canonical: docs/decisions/api-contract-governance.md
related:
  - docs/decisions/shared-documentation-strategy.md
  - fradelli/nodejs-sandicts-api:docs/ai/api/semantic-api-contracts.md
  - fradelli/nodejs-sandicts-api:docs/ai/api/error-handling-foundation.md
  - fradelli/reactjs-sandicts-web:docs/frontend/sandicts-frontend-tech-decisions.md
scope: api-contracts, openapi, compatibility, backend, frontend, ci
read-when:
  - adding or changing a public API endpoint
  - adding or changing a public error code
  - regenerating the frontend API client
  - deciding whether an API change is compatible
do-not-read-when:
  - changing backend internals with no public API impact
  - changing frontend presentation with no contract impact
---

# API Contract Governance

## Decision

The Sandicts backend runtime and its generated OpenAPI document are the exact
source of truth for public HTTP contracts.

The frontend consumes generated contracts through Orval and must not maintain
independent request, response, status, or public error-code catalogs.

Shared docs own cross-app compatibility and delivery rules. Backend and
frontend repositories own their implementation-specific details.

## Ownership

Backend owns:

- endpoint paths, methods, authentication, and cookie behavior
- semantic success and error statuses
- request, response, and public error schemas
- exact public error codes
- runtime-to-OpenAPI alignment
- the committed canonical OpenAPI artifact

Frontend owns:

- generated client output
- API request runtime and auth refresh coordination
- preserving normalized public error data
- semantic feature hooks and UI state mapping
- generated-client drift validation

Shared docs own:

- compatibility classification
- coordinated backend/frontend delivery expectations
- rules for deprecation and breaking changes

## Public Error Contract

All JSON errors use one stable envelope:

- `statusCode`
- `code`
- `message`
- `path`
- `timestamp`
- `requestId`
- optional `issues`

Rules:

- frontend behavior branches on `statusCode` and stable `code`
- `message` is safe human-readable context and is not a compatibility key
- unknown future codes are preserved and rendered through safe fallback UX
- validation issues remain structured and field-addressable
- tokens, cookies, credentials, provider payloads, stack traces, and internal
  details are never public

## Compatibility Classification

### Compatible

Normally compatible:

- adding a new endpoint
- adding an optional response field
- adding an optional request field with unchanged defaults
- improving documentation, examples, or descriptions without runtime changes
- adding a new response to a new endpoint

Generated clients should still be regenerated so committed output remains
current.

### Coordinated

Potentially breaking and requiring backend/frontend coordination:

- adding a new success status to an existing operation
- adding a new error status or public error code to an existing operation
- adding an enum member that frontend code may handle exhaustively
- changing authentication, refresh, cookie, retry, or rate-limit behavior
- changing a field from optional to nullable or the reverse

These changes require:

1. backend contract and OpenAPI update
2. frontend regeneration
3. representative runtime tests
4. release notes or Jira context describing the new branch

### Breaking

Breaking changes include:

- removing or renaming an endpoint, method, field, status, or public error code
- changing a field type
- making an optional field required
- changing the success status for an existing outcome
- moving an existing public error code to a different HTTP status
- changing an existing response body to an incompatible shape
- exposing a token in a body that was previously cookie-only

Breaking changes need an explicit migration decision. Prefer a compatibility
window or a versioned endpoint when existing clients cannot move atomically.

## Deprecation

Before removing a public contract:

1. mark the operation or field deprecated in OpenAPI when supported
2. document the replacement
3. regenerate the frontend client
4. migrate known consumers
5. remove only after the agreed compatibility window

Do not leave deprecated behavior undocumented or rely on message text to guide
client migration.

## Delivery Workflow

For a cross-app API change:

1. update backend runtime, schemas, exact response declarations, and tests
2. regenerate and verify the backend OpenAPI artifact
3. regenerate the frontend client from that artifact
4. update the frontend runtime or feature hooks when semantics changed
5. run backend and frontend contract checks
6. merge in an order that never leaves the frontend generated client claiming a
   contract the deployed backend does not provide

For coordinated changes, merge the backend contract first only when the
existing frontend has a safe fallback. Otherwise use a compatibility-first
backend change, then frontend adoption, then later cleanup.

## Success Responses

Sandicts does not use a universal success envelope.

Use endpoint-specific response bodies and semantic statuses:

- `200` for successful reads or synchronous commands returning a body
- `201` for resource creation
- `202` for accepted deferred work
- `204` for successful commands with no body

Exact endpoint behavior remains in backend OpenAPI.

## Drift Prevention

- backend CI regenerates the OpenAPI artifact and fails on a diff
- frontend CI regenerates Orval output and fails on a diff
- generated files are committed and never edited manually
- cross-stack tests cover success and representative `4xx`, `429`, and `5xx`
  responses
