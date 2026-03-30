---
name: creek-deploy
description: Deploy applications to Creek. Use when the user wants to deploy,
  publish, or ship their app, mentions "creek deploy", asks about preview URLs,
  sandbox deployments, or production releases on Creek.
license: Apache-2.0
compatibility: Requires Creek CLI (npm install -g @solcreek/cli)
metadata:
  author: solcreek
  version: "1.0"
---

# Deploy to Creek

Creek deploys web applications to Cloudflare Workers with a single command. It auto-detects your framework, builds your project, and provisions all necessary infrastructure.

## Deployment Modes

### 1. Authenticated Deploy (Permanent)

Requires `creek login` first. Deploys persist indefinitely under the user's account.

```bash
creek deploy              # Deploy current directory
creek deploy ./my-app     # Deploy specific directory
creek deploy --skip-build # Deploy pre-built output
```

### 2. Sandbox Deploy (Preview)

No authentication required. Creates a temporary 60-minute preview.

```bash
creek deploy              # Without login, auto-creates sandbox
creek claim SANDBOX_ID    # Claim a sandbox as permanent project (requires login)
```

### 3. Demo Deploy

Instant deployment of a pre-built demo to see Creek in action.

```bash
creek deploy --demo
```

### 4. Template Deploy

Deploy from a predefined template.

```bash
creek deploy --template vite-react
```

### 5. GitHub Repo Deploy

Deploy directly from a GitHub repository URL.

```bash
creek deploy https://github.com/user/repo
creek deploy https://github.com/user/repo --path packages/app  # Monorepo
```

## Typical First Deployment

```bash
creek login               # Authenticate (opens browser)
creek init                # Create creek.toml (optional, auto-detected)
creek deploy              # Deploy to preview
```

The deploy command will:
1. Auto-detect project config (`creek.toml` > `wrangler.*` > `package.json` > `index.html`)
2. Auto-detect framework and render mode (SPA / SSR / Worker)
3. Build the project
4. Upload static assets and SSR server (if applicable)
5. Return a preview URL

## Deploy Output

On success, Creek returns:
- **Preview URL**: `https://{id}.preview.creek.dev` (sandbox) or `https://{project}-{team}.bycreek.com` (authenticated)
- **Deployment ID**: For status tracking
- **Build duration**: Total deploy time

All commands support `--json` for structured output (auto-enabled in CI/CD):

```json
{
  "ok": true,
  "url": "https://my-app-team.bycreek.com",
  "deployDurationMs": 5000
}
```

## CI/CD Integration

Use `CREEK_TOKEN` environment variable for non-interactive deploys:

```bash
CREEK_TOKEN=ck_... creek deploy --yes
```

The `--yes` flag skips confirmation prompts (auto-enabled in non-TTY environments).

## Common Issues

| Problem | Solution |
|---------|----------|
| "Not authenticated" | Run `creek login` or set `CREEK_TOKEN` |
| "No creek.toml found" | Run `creek init` or deploy from project root |
| "No supported project found" | Use `--path` for monorepos |
| Sandbox expired | Sandboxes last 60 minutes — redeploy or use authenticated deploy |
