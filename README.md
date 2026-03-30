# Creek Skills

Official [Agent Skills](https://agentskills.io/) for [Creek](https://creek.dev) — the deployment platform that reduces Cloudflare's 200+ API primitives to a single command.

## Installation

```bash
npx skills add solcreek/creek-skills
```

This installs Creek skills into your AI agent (Claude Code, Cursor, Copilot, Gemini CLI, and [40+ others](https://agentskills.io/)).

## Available Skills

| Skill | Description |
|-------|-------------|
| [creek-deploy](skills/creek-deploy/) | Deploy applications to Creek |
| [creek-project-init](skills/creek-project-init/) | Initialize a new Creek project |
| [creek-config](skills/creek-config/) | Configure render modes and bindings |
| [creek-troubleshoot](skills/creek-troubleshoot/) | Diagnose common deployment issues |

## What is Creek?

Creek is a deployment platform built on Cloudflare Workers. It auto-detects your framework, determines the optimal render mode (SPA, SSR, or Worker), and deploys with per-tenant isolation — all from a single `creek deploy` command.

## License

Apache-2.0
