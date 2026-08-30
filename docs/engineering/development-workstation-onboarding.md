# Sandicts Development Workstation Onboarding

## Purpose

Use this guide to prepare a machine for ongoing Sandicts development with
Codex across the shared documentation, backend, and frontend repositories.
This is not a local deployment procedure and does not reproduce cloud
credentials or cloud configuration.

The target workspace is:

```text
sandicts/
  apps/
    sandicts-docs/
    nodejs-sandicts-api/
    reactjs-sandicts-web/
```

## What Is Local And What Is Shared

| Capability | Per-machine action | Source of truth that must not be copied |
| --- | --- | --- |
| Codex | Sign in and authorize the required plugins | Personal `~/.codex` or `%USERPROFILE%\.codex` state |
| GitHub | Authenticate GitHub CLI and the Codex GitHub plugin | Personal tokens and credential stores |
| Jira | Authorize the Codex Atlassian Rovo plugin when Jira work is needed | Personal Atlassian credentials |
| Repository instructions and skills | Clone or update the repositories | Versioned `AGENTS.md` and `.codex/skills` files |
| Local runtimes | Install Node.js, npm, Docker, and repository dependencies | Each repository's `.nvmrc`, `package.json`, and README |
| Local environment | Create ignored files from `.env.example` | Local values and secrets; never another machine's `.env` |
| Vercel | Optional login and link for a deployment operator only | Vercel project, environment variables, domains, and access controls |
| CI/CD and DNS | No workstation setup | GitHub Environments, Actions secrets/variables, Vercel, and Hostinger |

The versioned project skills are installed by cloning the repositories. Do not
copy a global Codex directory to reproduce them. The global directory may
contain authentication, history, sessions, and other personal state.

## Access Profiles

### Developer

The default profile requires:

- Codex access;
- GitHub repository access;
- GitHub CLI authentication;
- the Codex GitHub plugin;
- the Codex Atlassian Rovo plugin when the developer must read or update Jira;
- the local runtimes used by the repositories.

Vercel access is not required to implement, review, merge, or trigger CD. A
successful merge or push to a deployment branch triggers GitHub Actions from
GitHub-hosted runners after the CD workflows are present on that branch.

### Deployment Operator

This profile adds membership in the existing Vercel scope and may link a local
frontend checkout for platform inspection or administration. It does not
authorize manual deployments. Access assignment remains manual until Sandicts
introduces a separate access-management process.

## 1. Install And Authenticate The Workstation

Install:

- Codex desktop or CLI;
- Git;
- GitHub CLI;
- a Node.js version manager and Node.js 24;
- npm 11;
- Docker Desktop or Docker Engine with Compose.

Sign in to Codex. When using the CLI, verify the session with:

```bash
codex login status
```

Authenticate GitHub CLI without pasting a token into a command or file:

```bash
gh auth login
gh auth status
```

In Codex, install or enable the GitHub and Atlassian Rovo plugins, authorize
each service with the intended account, and start a new Codex chat after plugin
installation. If Jira is outside the developer's responsibilities, Atlassian
access may be omitted.

Do not copy `auth.json`, `config.toml`, history, sessions, browser profiles,
GitHub tokens, Vercel tokens, or the complete global `.codex` directory from
another machine. Personal Codex preferences should be configured locally.

## 2. Clone Or Align The Multi-Repository Workspace

For a new workspace:

```bash
mkdir sandicts
cd sandicts
mkdir apps
cd apps

gh repo clone fradelli/sandicts-docs
gh repo clone fradelli/nodejs-sandicts-api
gh repo clone fradelli/reactjs-sandicts-web
```

The expected integration branches are:

| Repository | Branch |
| --- | --- |
| `sandicts-docs` | `main` |
| `nodejs-sandicts-api` | `developer` |
| `reactjs-sandicts-web` | `developer` |

For repositories that already exist on the machine, preserve local work and
inspect it before updating:

```bash
git status -sb
git remote -v
git fetch --all --prune
```

Switch to the expected branch only when the worktree is clean, then update it
without creating a merge commit:

```bash
git switch <branch>
git pull --ff-only
```

To resume an open pull request, run this inside its repository instead of
creating a different task branch:

```bash
gh pr checkout <pr-number>
git status -sb
git pull --ff-only
```

Open the `apps` directory in Codex so the three sibling repositories are
available in the same workspace. Trust only the repositories whose remotes and
contents were verified.

## 3. Configure The Backend

The backend requirements are Node.js 24, npm 11, Docker, and Docker Compose.
Use `.nvmrc`, `package.json`, and `README.md` in `nodejs-sandicts-api` as the
current source of truth.

From the backend repository:

```bash
npm ci
```

Create the ignored local environment file. In PowerShell:

```powershell
Copy-Item .env.example .env
```

In a POSIX-compatible shell:

```bash
cp .env.example .env
```

Then start the local dependencies and application:

```bash
docker compose up -d postgres mailpit
npm run prisma:generate
npm run prisma:migrate:dev
npm run start:dev
```

Local endpoints:

- API: `http://localhost:3000`;
- Swagger: `http://localhost:3000/docs`;
- Mailpit: `http://localhost:8025`.

Read the backend README before changing environment values, database state,
authentication, or API behavior.

## 4. Configure The Frontend

The frontend repository defines Node.js `24.16.0` in `.nvmrc` and npm `11.13.0`
in `package.json`. Those repository files remain the source of truth if the
versions change.

From the frontend repository:

```bash
npm ci
```

Create the ignored local environment file. In PowerShell:

```powershell
Copy-Item .env.example .env.local
```

In a POSIX-compatible shell:

```bash
cp .env.example .env.local
```

Keep the example's local URLs and provide only locally authorized values. Then
start the application:

```bash
npm run dev
```

The frontend runs at `http://localhost:3001` and expects the local API at
`http://localhost:3000`. Install Playwright's browser only when local E2E tests
are needed:

```bash
npx playwright install chromium
```

## 5. Optional Vercel Access

Skip this section for the Developer profile. The application can be developed
and CD can run without a local Vercel session.

A Deployment Operator may install the same pinned CLI version used by CD and
link the checkout to the existing project:

```bash
npm install --global vercel@58.4.4
vercel login
vercel link --project sandicts-web --scope developerlucaslimas-projects
vercel whoami
```

Select the existing `sandicts-web` project. Linking may create a local
`.vercel/project.json`; `.vercel` must remain ignored and unversioned.

Do not:

- create another Vercel project;
- run `vercel link --repo`;
- enable the Vercel Git integration;
- execute `vercel deploy` merely to validate the workstation;
- copy `.vercel`, a Vercel token, or downloaded Vercel environment files
  between machines.

GitHub Actions uses its own GitHub Environments, secrets, and variables. The
local link neither configures nor replaces CD.

## 6. Verification

Verify the local tools:

```bash
gh auth status
node --version
npm --version
docker --version
docker compose version
```

Verify the integrations from Codex:

- the GitHub plugin can read a known Sandicts repository or pull request;
- the Atlassian Rovo plugin can read a known `KAN-*` issue when Jira access is
  part of the profile;
- the three repositories and their versioned project skills are visible;
- each repository's `git status -sb` shows only intentional work.

Verify the applications by starting the backend and frontend and opening their
local endpoints. Do not use a production or preview deployment as a substitute
for local onboarding validation.

## Prompt For Codex On A New Machine

Use this prompt after cloning or opening the workspace:

```text
Quero preparar esta máquina para continuar o desenvolvimento multi-repo do
Sandicts com Codex, sem executar deploy.

Leia completamente:
- sandicts-docs/AGENTS.md;
- sandicts-docs/docs/engineering/development-workstation-onboarding.md;
- o README e as instruções versionadas de cada repositório envolvido.

Primeiro faça uma auditoria somente leitura do que já existe nesta máquina.
Depois alinhe Codex, GitHub CLI, plugins GitHub/Atlassian, runtimes, dependências
e ambientes locais conforme o guia. Preserve qualquer trabalho local.

Vercel é opcional e serve apenas para administração da plataforma. Não faça
deploy, não crie projeto, não conecte integração Git, não altere GitHub Actions,
Jira, Vercel ou DNS e não copie nem solicite secrets, .env, .vercel ou o diretório
global .codex. Pare apenas quando uma autenticação interativa precisar de mim e,
ao final, apresente o checklist de validação.
```

For a specific open task or pull request, add its repository, Jira key, pull
request number, and expected branch to the prompt. Do not make current PR
numbers part of this permanent guide.
