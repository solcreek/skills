# Agent Skills for Creek — DEPRECATED

> This repository is **deprecated and frozen**. The Creek skill has
> moved into the main Creek monorepo and is now maintained alongside
> the CLI, SDK, and MCP server.

## Switch to the new install URL

```bash
npx skills add solcreek/creek/skills
```

The `skills` CLI supports subpath installation, so the new URL maps
directly to the `skills/` directory inside the Creek monorepo at
[solcreek/creek](https://github.com/solcreek/creek).

## Why moved

Three channels used to consume the same skill content from different
places:

- the filesystem skill installed via `npx skills add`
- the MCP resources served by `mcp.creek.dev`
- the llms.txt agent playbook served from creek.dev

Keeping a separate repo as the source of truth forced manual or CI
sync to the other consumers — fragile, PAT-dependent, silently
drift-prone. Consolidating everything into the main monorepo makes
drift architecturally impossible: one `.md` edit, one deploy, all
surfaces updated.

## What's still here

The last snapshot of the skill content before consolidation. No
further updates will land in this repo. The content may be
inaccurate relative to the latest `creek` CLI; follow the new URL.

## Links

- **New install**: `npx skills add solcreek/creek/skills`
- **Monorepo location**: [solcreek/creek/tree/main/skills](https://github.com/solcreek/creek/tree/main/skills)
- **MCP server**: `https://mcp.creek.dev/mcp` (exposes the same reference content as `creek://skill/*` resources)
- **Creek docs**: [creek.dev/docs](https://creek.dev/docs)
