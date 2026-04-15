# Agent Skills for Creek

[Agent Skills](https://agentskills.io/) for [Creek](https://creek.dev) — the deployment platform that reduces Cloudflare's 200+ API primitives to a single command.

```
npx skills add solcreek/skills
```

See [install.md](install.md) for full setup instructions including CLI installation.

## Available skills

| Skill | Description |
|-------|-------------|
| [creek](skills/creek/SKILL.md) | Deploy and manage applications on Creek — deploy, diagnose failures, read logs, manage team databases, domains, cron, queues, GitHub push deploys. |

## Skill layout

The `creek` skill follows Anthropic's [progressive-disclosure guidance](https://code.claude.com/docs/en/skills) — `SKILL.md` stays lean (~120 lines with mental model, anti-patterns, quick triage, cheat sheet), and topic-focused detail lives in `references/` files that Claude loads on-demand:

```
skills/creek/
├── SKILL.md                     entry + index of references
└── references/
    ├── commands.md              full command table + JSON output spec
    ├── deployment-modes.md      authenticated / sandbox / CI / --from-github
    ├── workflows.md             first-deploy / rollback / domain, frameworks
    ├── creek-toml.md            creek.toml reference + cron + queue
    ├── diagnosis.md             failure workflow + CK-code map + troubleshooting
    ├── observability.md         creek logs + build logs + MCP get_build_log
    ├── resources.md             creek db + team-owned databases + portable pattern
    └── github-setup.md          GitHub App install + connection
```

## Requires

- [Creek CLI](https://www.npmjs.com/package/creek) (`npm install -g creek`)
- An authenticated account for production deploys (`creek login`)

## License

Apache-2.0
