---
name: creek
description: |
  Deploy and manage applications on Creek — the Cloudflare Workers deployment
  platform. Covers init, deploy (local + GitHub + remote trigger), status,
  projects, deployments, rollback, env vars, custom domains, cron + queue
  triggers, queue send, and the dev server. Use when the user mentions creek,
  creek deploy, creek init, deploying to Cloudflare, or wants to configure,
  inspect, or troubleshoot a Creek project.
license: Apache-2.0
compatibility: Requires Creek CLI (npm install -g creek)
metadata:
  author: solcreek
  version: "2.3"
  required-binaries: creek
  required-env: CREEK_TOKEN
---

# Creek CLI — Agent Skill

Creek deploys web apps to Cloudflare Workers with a single command. Auto-detects framework, determines render mode (SPA/SSR/Worker), provisions infrastructure.

## Mental Model

Creek runs on Cloudflare Workers, but **it is not raw Cloudflare**. The
resource model, deploy flow, and runtime are abstracted. An agent
reaching for CF-native workarounds (wrangler.toml edits, manual D1
creation, driver rewrites) is almost always wrong on Creek.

The rule of thumb: if you're about to write CF-specific glue code, check
whether the `creek` CLI or `@solcreek/runtime` already covers the concern.
If yes — use the Creek path.

## What you DON'T need to do on Creek

Because Creek visibly runs on CF, it's tempting to apply CF reasoning.
Don't. These five shortcuts are all counterproductive:

- **Do NOT swap `better-sqlite3` → D1 manually before deploy.** Creek's
  runtime routes the same driver API to `better-sqlite3` locally and D1
  in production. One code path, one schema, one migration set. If the
  user's repo imports `better-sqlite3`, ship it unchanged.

- **Do NOT maintain separate sandbox/production code paths.** Env var
  behavior is identical. Sandbox just runs without user-set secrets. Gate
  on `env.MY_KEY` being present; deploy to production via `creek deploy`
  when the full env is needed.

- **Do NOT hand-edit `wrangler.toml`.** Creek reads `creek.toml` and
  generates wrangler config at build time. Bindings are declared in the
  dashboard or via `creek db attach` — not in wrangler.toml.

- **Do NOT create a D1 via `wrangler d1 create`.** Use `creek db create
  <name>` — it creates a team-owned resource that can be renamed, shared
  across projects, and detached without dropping data.

- **Do NOT split `server/db.local.ts` + `server/db.prod.ts`.** Use the
  single-file portable pattern from `examples/vite-react-drizzle`. The
  `creek doctor` rule `CK-DB-DUAL-DRIVER-SPLIT` fires when you have
  the split files.

## Agent Rules

1. **Always use `--json`** for structured output. Auto-enabled in non-TTY / CI.
2. **Follow `breadcrumbs`** in JSON responses — they suggest the next command.
3. **Use `--yes`** to skip confirmation prompts (auto-enabled in non-TTY).
4. **Check `ok` field** — `true` = success, `false` = error with `error` and `message` fields.

## Command Reference

| Task | Command |
|------|---------|
| Authenticate | `creek login` |
| Authenticate (CI) | `creek login --token <KEY>` |
| Check auth | `creek whoami --json` |
| Init project | `creek init --json` |
| Deploy | `creek deploy --json` |
| Deploy directory | `creek deploy ./dist --json` |
| Deploy from GitHub URL | `creek deploy https://github.com/user/repo --json` |
| Deploy monorepo subdir | `creek deploy https://github.com/user/repo --path packages/app --json` |
| Deploy latest commit via GitHub connection | `creek deploy --from-github --json` |
| Deploy latest (target specific project) | `creek deploy --from-github --project <SLUG> --json` |
| Deploy demo | `creek deploy --demo --json` |
| Deploy template | `creek deploy --template vite-react` |
| Skip build | `creek deploy --skip-build --json` |
| Check status | `creek status --json` |
| Check sandbox | `creek status <SANDBOX_ID> --json` |
| Claim sandbox | `creek claim <SANDBOX_ID> --json` |
| List projects | `creek projects --json` |
| List deployments | `creek deployments --json` |
| List deployments (other) | `creek deployments --project <SLUG> --json` |
| Rollback | `creek rollback --json` |
| Rollback to specific | `creek rollback <DEPLOYMENT_ID> --json` |
| Set env var | `creek env set <KEY> <VALUE> --json` |
| List env vars | `creek env ls --json` |
| Show env values | `creek env ls --show --json` |
| Remove env var | `creek env rm <KEY> --json` |
| Add domain | `creek domains add <HOSTNAME> --json` |
| List domains | `creek domains ls --json` |
| Activate domain | `creek domains activate <HOSTNAME> --json` |
| Remove domain | `creek domains rm <HOSTNAME> --json` |
| Send a message to the project queue | `creek queue send '<JSON-BODY>' --json` |
| Dev server (local) | `creek dev` |
| Dev server + trigger a cron firing | `creek dev --trigger-cron "*/5 * * * *"` |

## Deployment Modes

### Authenticated (permanent)
Requires `creek login`. Deploys persist under the user's account.
Builds locally, uploads the bundle, deploys to Workers for Platforms.
```bash
creek deploy --json
```

### Sandbox (60-min preview)
No auth required. Temporary preview with claimable URL.
```bash
creek deploy --json          # auto-sandbox when not logged in
creek claim <SANDBOX_ID>     # convert to permanent project
```

### CI/CD
```bash
CREEK_TOKEN=ck_... creek deploy --yes --json
```

### Remote build via GitHub connection
When the project has a GitHub connection (set up via the dashboard's
`/new` import flow or `/projects/:id/settings`), Creek can deploy the
latest commit on the project's production branch **without** running
the build locally. The control-plane fetches the commit, invokes the
same remote-builder container used by `git push` webhooks, and runs
the full deploy pipeline server-side.

Use this when:
- you want to redeploy a project from a machine that doesn't have
  the source checked out (fresh CI runner, different laptop, an
  agent with a narrow workspace)
- you want to trigger a deploy without actually pushing a commit
- you want CI capability parity with the dashboard "Deploy latest"
  button

```bash
# Infer the project from creek.toml in cwd
creek deploy --from-github --json

# Or target an explicit project by slug (or UUID)
creek deploy --from-github --project my-app --json
```

The command polls the deployments list until the new row settles on
`active`, `failed`, or `cancelled`, and prints each status transition.
Fails fast if the project has no github_connection or the connection's
production branch has no accessible commit.

## JSON Output Format

Every command returns structured JSON with breadcrumbs:

```json
{
  "ok": true,
  "url": "https://my-app-team.bycreek.com",
  "project": "my-app",
  "breadcrumbs": [
    { "command": "creek status", "description": "Check deployment status" },
    { "command": "creek deployments --project my-app", "description": "View deployment history" }
  ]
}
```

On error:
```json
{
  "ok": false,
  "error": "not_authenticated",
  "message": "Not authenticated. Run `creek login` first.",
  "breadcrumbs": [
    { "command": "creek login", "description": "Authenticate interactively" }
  ]
}
```

## Workflow: First Deploy

```bash
creek login --json                # 1. Authenticate
creek init --json                 # 2. Create creek.toml (optional)
creek deploy --json               # 3. Deploy
```

## Workflow: Update & Rollback

```bash
creek deploy --json               # Deploy new version
creek deployments --json          # View history
creek rollback --json             # Rollback to previous
creek rollback <ID> --json        # Rollback to specific deployment
```

## Workflow: Custom Domain

```bash
creek domains add app.example.com --json     # Add domain
# User sets DNS: CNAME app.example.com → cname.creek.dev
creek domains activate app.example.com --json # Activate after DNS
creek domains ls --json                       # Verify status
```

## creek.toml Reference

```toml
[project]
name = "my-app"              # Required. Lowercase alphanumeric + hyphens.
framework = "nextjs"         # Optional. Auto-detected from package.json.

[build]
command = "npm run build"    # Build command (default: npm run build)
output = "dist"              # Build output directory
worker = "worker/index.ts"   # Optional: custom Worker entry point

[resources]
database = true              # D1 database   → env.DB    + `import { db } from 'creek'`
storage  = true              # R2 bucket     → env.BUCKET + `import { storage } from 'creek'`
cache    = true              # KV namespace  → env.KV    + `import { cache } from 'creek'`
ai       = true              # Workers AI    → env.AI    + `import { ai } from 'creek'`

[triggers]
cron  = ["0 */6 * * *"]      # One or more cron expressions. Standard 5-field format.
queue = true                 # Creates a per-project queue + consumer binding.
                             # Producer: `import { queue } from 'creek'` inside the Worker.
                             # Consumer: export a `queue(batch, env, ctx)` handler.
```

**Semantic names** (`database` / `storage` / `cache`) are the stable
Creek names and what you should write in new projects. The legacy
Cloudflare names (`d1` / `r2` / `kv`) are still accepted for
backward compatibility but deprecated.

### Cron triggers

Creek reads the `cron` array and registers each expression with
Cloudflare's scheduled event system. The Worker must export a
`scheduled(event, env, ctx)` handler. Creek generates this handler
automatically when using a framework; for hand-written Workers, see
the SSR/Worker examples in the docs.

Verify cron is active:
```bash
creek status --json          # Shows registered cron schedules
```

Simulate a firing locally during dev:
```bash
creek dev --trigger-cron "0 */6 * * *"
```

### Queue triggers

Setting `queue = true` auto-provisions a Cloudflare Queue and binds
it to the project. Inside the Worker:

```ts
// Producer — in any request handler
import { queue } from 'creek';
await queue.send({ userId: 'abc', action: 'welcome-email' });
```

```ts
// Consumer — export from the Worker entry
export default {
  async queue(batch, env, ctx) {
    for (const msg of batch.messages) {
      console.log(msg.body);
      msg.ack();
    }
  },
};
```

Send a message from the CLI (useful for testing):
```bash
creek queue send '{"userId":"abc"}' --json
```

## Supported Frameworks

**SPA**: vite-react, vite-vue, vite-svelte, vite-solid, static HTML, astro
**SSR**: nextjs, react-router, sveltekit, nuxt, solidstart, tanstack-start

Not every SSR framework has equal support yet — check
[creek.dev/docs/getting-started](https://creek.dev/docs/getting-started)
for the current compatibility matrix. Next.js in particular requires
`@solcreek/adapter-creek` or Creek's OpenNextJS workaround.

## Config Detection Order

1. `creek.toml` — explicit Creek config
2. `wrangler.jsonc` / `wrangler.json` / `wrangler.toml` — existing CF config
3. `package.json` — framework auto-detection
4. `index.html` — static site

## GitHub Auto-Deploy Setup

For push-to-deploy + PR previews, the user installs the **Creek
Deploy** GitHub App and connects a repository to a Creek project:

1. Visit `https://github.com/apps/creek-deploy` and install on the
   account or organization that owns the repo.
2. GitHub redirects to `https://app.creek.dev/github/setup?installation_id=...`
   which claims the installation for the current team and lists the
   installation's repositories.
3. Either import a new project from the dashboard's New Project →
   Import Git Repository flow, or connect an existing project from
   its Settings → GitHub Connection section.
4. Pushes to the project's production branch trigger a full build +
   deploy; pull requests trigger a preview deployment and post a
   commit status with the preview URL.
5. `creek deploy --from-github [--project <slug>]` can trigger the
   same flow for the latest commit on demand (no push required).

## Troubleshooting

| Error | Fix |
|-------|-----|
| "Not authenticated" | `creek login` or set `CREEK_TOKEN` |
| "Invalid API key" | `creek login` to re-authenticate |
| "No creek.toml found" | `creek init` or cd to project root |
| "No project found" | Deploy from a dir with package.json or index.html |
| "No supported project found in repo" | Use `--path` for monorepos |
| "Project has no GitHub connection" (on `--from-github`) | Connect the repo first via the dashboard Settings → GitHub Connection |
| "Could not determine target project" (on `--from-github`) | Pass `--project <slug>` or run the command from a directory with a `creek.toml` |
| Sandbox expired | Redeploy — sandboxes last 60 minutes |
| Domain stuck "pending" | Set CNAME to `cname.creek.dev`, then `creek domains activate` |
| Build fails | Check `[build] command` in creek.toml |
| Webhook not firing on push | Verify the repo is connected under project Settings; GitHub App must be installed on the repo's account |
