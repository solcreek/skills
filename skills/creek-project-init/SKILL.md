---
name: creek-project-init
description: Initialize a new Creek project or scaffold from a template. Use when
  the user wants to create a new project, start a new app, set up Creek for the
  first time, or asks about "creek init" or "create-creek-app".
license: Apache-2.0
compatibility: Requires Creek CLI (npm install -g @solcreek/cli)
metadata:
  author: solcreek
  version: "1.0"
---

# Initialize a Creek Project

## Two Ways to Start

### 1. `creek init` — Add Creek to an Existing Project

Creates a `creek.toml` configuration file in the current directory.

```bash
creek init
creek init --name my-app
```

Creek auto-detects the framework from `package.json` and sets sensible defaults. The generated `creek.toml` looks like:

```toml
[project]
name = "my-app"
framework = "vite-react"    # Auto-detected

[build]
command = "npm run build"
output = "dist"

[resources]
d1 = false
kv = false
r2 = false
ai = false
```

### 2. `create-creek-app` — Scaffold a New Project

```bash
npx create-creek-app my-app
```

This scaffolds a new project with Creek pre-configured.

## Supported Frameworks

### SPA Frameworks (Static Output)
- **vite-react** — Vite + React
- **vite-vue** — Vite + Vue
- **vite-svelte** — Vite + Svelte
- **vite-solid** — Vite + Solid.js

### SSR Frameworks (Server-Side Rendering)
- **nextjs** — Next.js (uses `@opennextjs/cloudflare` adapter)
- **react-router** — React Router v7+
- **sveltekit** — SvelteKit
- **nuxt** — Nuxt 3
- **solidstart** — SolidStart
- **tanstack-start** — TanStack Start

### Static Sites
- No framework — serves plain HTML/CSS/JS

## Config Detection Chain

Creek finds project configuration in this order (first match wins):
1. `creek.toml` — Explicit Creek config
2. `wrangler.jsonc` / `wrangler.json` / `wrangler.toml` — Existing Cloudflare config
3. `package.json` — Framework auto-detection
4. `index.html` — Static site

## Project Name Rules

- Lowercase letters, numbers, and hyphens only (`^[a-z0-9-]+$`)
- No spaces or uppercase letters

## After Initialization

```bash
creek dev      # Start local dev server (port 3000)
creek deploy   # Deploy to Creek
```

See [FRAMEWORKS.md](references/FRAMEWORKS.md) for framework-specific default build outputs.
