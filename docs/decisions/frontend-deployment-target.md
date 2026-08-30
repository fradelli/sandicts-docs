---
title: Frontend Deployment Target
doc-type: cross-app-architecture-decision
role: source-of-truth
priority: high
canonical: docs/decisions/frontend-deployment-target.md
related:
  - docs/decisions/shared-documentation-strategy.md
  - docs/decisions/api-contract-governance.md
  - fradelli/reactjs-sandicts-web:docs/frontend/sandicts-deployment-environments.md
  - fradelli/nodejs-sandicts-api:docs/ai/config/configuration-foundation.md
scope: frontend, backend, deployment, environments, vercel, cors, cookies, auth
read-when:
  - configuring frontend or backend deployment environments
  - deciding preview, staging, or production origins
  - changing browser CORS, cookies, or authentication callbacks
  - planning KAN-64, KAN-29, KAN-30, KAN-27, or KAN-28
do-not-read-when:
  - changing only local UI or backend business logic with no deployment impact
---

# Frontend Deployment Target

## Status

Accepted and configured for the MVP under KAN-64 and KAN-29.

Ownership of `sandicts.com` and `sandicts.com.br` is confirmed. Vercel domain
verification, TLS, Hostinger DNS, the canonical apex, Preview, and production
redirects are configured. The first workflow-driven deployments and the Render
API services remain pending; backend implementation and API URLs are owned by
KAN-30.

## Decision

Use Vercel for the Sandicts Next.js frontend.

Use three runtime tiers:

- local development
- stable preview integration on the `staging` branch
- production on the `master` branch

Feature, fix, and `developer` pushes do not deploy. GitHub Actions is the only
CI/CD orchestrator and calls the existing CI before deploying the exact
post-merge `staging` or `master` SHA through pinned Vercel CLI. The Vercel Git
integration and automatic Git deployments are not used.

The fixed preview origin validates full-stack authentication before promotion
to production.

## Provider Comparison

| Provider | Decision | Reason |
| --- | --- | --- |
| Vercel | Selected | Native Next.js lifecycle, CLI prebuilt deployments, custom domains, environment scoping, and rollback with no application adapter |
| Netlify | Fallback | Viable Next.js support through OpenNext, but adds an adapter layer for this stack |
| Cloudflare Workers | Future option | Strong edge platform, but requires OpenNext/Workers compatibility and more runtime-specific configuration |
| AWS Amplify | Not selected | Its documented Next.js support currently trails the repository's Next.js major |
| Self-hosting | Not selected for MVP | Requires reverse proxy, cache/CDN, release, scaling, and multi-instance operational ownership |

Keep application environment variables provider-neutral. Do not introduce
Vercel-specific business or authentication logic.

## Environment Matrix

Confirmed frontend origins and reserved API origins:

| Tier | Frontend | API | Browser auth |
| --- | --- | --- | --- |
| local | `http://localhost:3001` | `http://localhost:3000` | complete |
| stable preview | `https://preview.sandicts.com.br` | `https://api.preview.sandicts.com.br` | complete |
| production | `https://sandicts.com.br` | `https://api.sandicts.com.br` | complete |

`sandicts.com`, `www.sandicts.com`, and `www.sandicts.com.br` are secondary
production domains that redirect to `https://sandicts.com.br`. Hostinger
remains authoritative for DNS; the exact Vercel A, CNAME, or verification
records must be copied from `vercel domains inspect` rather than hard-coded in
documentation.

If the frontend later moves from the apex to `app.sandicts.com.br`, treat it as
a coordinated migration across canonical metadata, CORS, cookies, Google
origins, magic-link base URL, redirects, DNS, and tests.

## CORS And Credentials

- browser API requests use `credentials: "include"`
- API responses use an exact allowed origin and credentials
- wildcard credentialed CORS is forbidden
- local allows exactly `http://localhost:3001`
- stable preview and production allow only their fixed frontend origin
- generated Vercel origins are not part of the deployed allowlist

## Cookie Contract

The refresh token cookie is:

- `HttpOnly`
- `SameSite=Lax`
- `Secure=false` locally
- `Secure=true` in stable preview and production
- `Path=/auth/refresh`
- host-only, with no `Domain` attribute

Stable preview and production keep frontend and API on HTTPS subdomains of the
same registrable domain. This preserves same-site cookie behavior while CORS
handles the cross-origin browser request.

`SameSite=None` is not part of the MVP deployment design. Introducing it
requires a new CSRF and third-party-cookie decision.

## Authentication

### Magic Link

`WEB_APP_BASE_URL` points to the local, stable preview, or production frontend.
The callback route is `/sign-in/magic-link`.

### Google

Authorized JavaScript origins are:

- local frontend
- fixed stable preview frontend
- production frontend

Generated Vercel origins are excluded. Explicit Google Sign-In and One Tap
exchange the provider credential through the same backend endpoint and create
the same Sandicts session.

### Refresh And Logout

Refresh remains a direct browser request to the Nest API. The access token stays
in memory. Logout revokes the backend session and clears the cookie with the
same attributes used when it was issued.

All provider success paths use the common safe post-login resolver. Redirect
destinations accept internal application paths only.

## Environment Ownership

Frontend public/build variables:

- `NEXT_PUBLIC_APP_ENV`
- `NEXT_PUBLIC_API_BASE_URL`
- `NEXT_PUBLIC_AUTH_ENABLED`
- `NEXT_PUBLIC_GOOGLE_CLIENT_ID`
- `NEXT_PUBLIC_GOOGLE_ONE_TAP_ENABLED`
- `WEB_ORIGIN`
- `SEO_INDEXING_ENABLED`
- `OPENAPI_SCHEMA_URL`

Backend deployment variables:

- `APP_ENV`
- `CORS_ALLOWED_ORIGINS`
- auth token and cookie settings
- `AUTH_GOOGLE_CLIENT_ID`
- `WEB_APP_BASE_URL`
- email provider settings
- database, logging, docs, and observability settings

Repository examples contain names and safe local placeholders only. Real
secrets live in deployment-provider secret stores.

## CI/CD Contract

The frontend deployment contract is:

- feature, fix, and `developer` pushes create no Vercel deployment
- `staging` calls the reusable CI, checks out the approved SHA, runs
  `vercel pull --environment=preview`, builds and deploys prebuilt artifacts,
  then assigns `preview.sandicts.com.br` with `vercel alias set`
- `master` calls the same CI, checks out the approved SHA, runs
  `vercel pull --environment=production`, builds with `--prod`, and deploys
  prebuilt Production artifacts with `--prod`
- GitHub Environments `preview` and `production` restrict deployment branches
  and access to `VERCEL_TOKEN`
- `VERCEL_ORG_ID` and `VERCEL_PROJECT_ID` are GitHub variables
- Vercel CLI is pinned rather than installed from `latest`
- Preview receives no production secrets or production data

Protected branches accept only the forward promotion path
`developer -> staging -> master`. Every promotion PR records its release type,
exact source SHA, target environment, Jira scope, and rollback plan. Production
promotion additionally records the stable Preview deployment and validation
evidence for the same release. Feature, security, and recovery corrections
enter through a temporary Jira branch targeting `developer`; neither `staging`
nor `master` is patched directly.

Full browser auth is validated locally and on the stable preview after
promotion to `staging`.

## Dependencies

- KAN-29 implements the Vercel project and preview/production settings
- KAN-30 plans and configures the Render backend target
- KAN-27 owns stable preview/staging operational configuration
- KAN-28 owns production operational configuration
- KAN-125 consumes the final production origin for SEO
- Auth and E2E tasks validate magic link, Google, One Tap, refresh, logout, and
  expired-session behavior

## Rollback

- promote the last healthy Vercel deployment
- restore the previous environment-variable version
- revert custom-domain DNS only when its target changed
- preserve refresh-cookie name and path during emergency rollback
- never widen CORS to a wildcard as a recovery measure
- reconcile every emergency rollback with a Jira-tracked revert or fix through
  `developer -> staging -> master`

## Follow-Up Decisions

- plan and deploy the Preview and Production Render services in KAN-30
- configure and validate `api.preview.sandicts.com.br` and
  `api.sandicts.com.br`
- validate the first workflow-driven Vercel Preview and Production deployments
- define preview data reset/isolation
- move backend rate limiting to shared storage before multiple API instances
