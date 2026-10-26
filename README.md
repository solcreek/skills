# solcreek agent skills

[Agent Skills](https://agentskills.io/) for solcreek's tools, and the Claude Code
plugin marketplace that lists them.

| skill | for | source |
|---|---|---|
| [creek](#creek) | deploying and running apps on [Creek](https://creek.dev) | [`skills/creek`](skills/creek) in this repository |
| [linearctl](#linearctl) | reading and changing [Linear](https://linear.app) issues with the `linearctl` CLI | [`skills/linearctl`](https://github.com/solcreek/linear-cli/tree/main/skills/linearctl) in solcreek/linear-cli, generated from the CLI's own help |

## Install

### Claude Code

```
/plugin marketplace add solcreek/skills
/plugin install creek@solcreek
/plugin install linearctl@solcreek
```

Each plugin tracks its repository's commits. To receive them on their own,
turn on updates once: `/plugin` → **Marketplaces** → `solcreek` → **Enable
auto-update**; or run `/plugin marketplace update solcreek` when you want them.

### Other agents

The [`skills`](https://skills.sh) CLI installs into Cursor, GitHub Copilot,
Windsurf, Codex, Cline, Gemini CLI, Claude Code and 30+ other agents:

```bash
npx skills add solcreek/skills        # creek
npx skills add solcreek/linear-cli    # linearctl
```

## creek

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

For agents that support MCP, Creek also provides a remote MCP server at `mcp.creek.dev/mcp` with tools (deploy, resource CRUD, DB query, build log inspection) and the same reference content as `creek://skill/*` resources.

[install.md](install.md) is a step-by-step prompt an agent can follow to install the Creek CLI and this skill.

## linearctl

An agent-first CLI for Linear: list, search, show, create and update issues,
comments, projects and relations, with `--json` everywhere and `--dry-run` /
`--yes` on every change. The skill carries the rules an agent needs (rerun-safe
creates, exit codes, which fields hold other people's text) and each command's
`--help`.

It lives in [solcreek/linear-cli](https://github.com/solcreek/linear-cli), not
here: it is generated from the CLI's own `describe` and `--help`, and that
repository's tests fail when it is stale, so it cannot drift from the commands.
This marketplace points at it.

## How skills load

Skills follow progressive disclosure:

1. **Discovery** — agent reads only `name` + `description` from SKILL.md frontmatter (low context cost)
2. **Activation** — when a task matches, the full SKILL.md loads into context
3. **Deep dive** — agent reads `references/*.md` on demand for detailed guidance

## Adding a skill

A skill whose tool generates its documentation should live with that tool and
be listed in [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json)
with a `github` source, as linearctl is. A hand-written skill can live in
`skills/<name>/` here, listed with `"source": "./"` and its `skills` path, as
creek is. Check the catalog with `claude plugin validate .`.

## Links

- [Creek](https://creek.dev) · [Creek CLI docs](https://creek.dev/docs) · [MCP server](https://mcp.creek.dev/mcp)
- [linearctl](https://github.com/solcreek/linear-cli)
- [Agent Skills spec](https://agentskills.io)

## License

Apache-2.0 for this repository. linearctl and its skill are MIT, in their own repository.
