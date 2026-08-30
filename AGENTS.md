## Connector Usage

### Jira / Atlassian

- When the user provides an explicit Jira key such as `KAN-93`, do not start with Rovo/global search.
- Use the direct Jira flow:
  1. Get accessible Atlassian resources.
  2. Reuse the Sandicts cloudId when available: `220c9326-0c6e-4824-a0ad-bdcb600e1932`.
  3. Fetch the issue directly by key with `getJiraIssue`.
- Use Rovo/global search only when there is no explicit Jira key, or when searching broad company knowledge.

### GitHub

- Sandicts app workspaces are commonly multi-repo. Do not assume the workspace root is the GitHub repo.
- Resolve the target repo from the explicit user request or from the subproject `git remote -v`.
- Known repositories:
  - `fradelli/nodejs-sandicts-api`
  - `fradelli/reactjs-sandicts-web`
  - `fradelli/sandicts-docs`
- When repo, PR, issue, branch, or URL is known, call the repo-specific GitHub tool directly.
- Use broad GitHub search only for file/content discovery when the target is not explicit.

### Connector Noise

- Keep connector narration minimal.
- Do not narrate expected fallback attempts.
- Report only confirmed blockers, permission failures, or decisions that affect the task.

### Development Workstation

- For new-machine or workstation-alignment requests, read `docs/engineering/development-workstation-onboarding.md`.
- Keep shared setup versioned, but never copy or request credential stores, tokens, local `.env` files, `.vercel`, or the user's global `.codex` directory.
