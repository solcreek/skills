# Agent Skills for Creek

Official [Agent Skills](https://agentskills.io/) for [Creek](https://creek.dev) — the open-source deployment platform.

## Install

```bash
npx skills add solcreek/skills
```

Works with Claude Code, Cursor, GitHub Copilot, Windsurf, Codex, Cline, Gemini CLI, and 30+ other AI coding agents.

## Available Skills

### creek

Deploy and manage apps on Creek. Covers the full CLI surface: deploy (7 modes), local dev, environment variables, database/storage/cache provisioning, custom domains, logs, metrics, rollback, diagnostics, and creekd operations.

```
skills/creek/
  SKILL.md                      # Main skill manifest
  references/
    commands.md                 # Complete command table + JSON output spec
    deployment-modes.md         # Authenticated / sandbox / CI / remote-GitHub
    workflows.md                # First-deploy, update-rollback, custom-domain
    creek-toml.md               # creek.toml reference, cron + queue
    diagnosis.md                # CK-code fix table + failure workflows
    observability.md            # creek logs + build logs + edge-cache caveats
    resources.md                # creek db + team-owned resource model
    github-setup.md             # GitHub App install + push-to-deploy
```

## How It Works

Skills follow progressive disclosure:

1. **Discovery** — agent reads only `name` + `description` from SKILL.md frontmatter (low context cost)
2. **Activation** — when a task matches, the full SKILL.md loads into context
3. **Deep dive** — agent reads `references/*.md` on demand for detailed guidance

## MCP Server

For agents that support MCP, Creek also provides a remote MCP server at `mcp.creek.dev/mcp` with tools (deploy, resource CRUD, DB query, build log inspection) and the same reference content as `creek://skill/*` resources.

## Links

- [Creek](https://creek.dev)
- [Creek CLI docs](https://creek.dev/docs)
- [Agent Skills spec](https://agentskills.io)
- [MCP server](https://mcp.creek.dev/mcp)

## License

Apache-2.0
