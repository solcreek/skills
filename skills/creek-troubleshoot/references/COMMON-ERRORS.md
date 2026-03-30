# Common Error Patterns

## Build Output Mismatch

The most common deployment failure is a mismatch between the actual build output directory and what Creek expects.

### Default Output Directories by Framework

| Framework | Expected Output |
|-----------|----------------|
| `nextjs` | `.open-next/` |
| `react-router` | `build/client/` |
| `sveltekit` | `.svelte-kit/output/client/` |
| `solidstart` | `.output/public/` |
| `nuxt` | `.output/public/` |
| `tanstack-start` | `dist/` |
| Vite-based (react/vue/svelte/solid) | `dist/` |
| Static sites | `dist/` |

**Fix**: If your build outputs to a non-standard directory, set it explicitly:

```toml
[build]
output = "build"
```

## Next.js Specific

### SSR vs Static Export

- **SSR mode** (default): Creek auto-installs `@opennextjs/cloudflare` and deploys server + static assets
- **Static export**: Set `output: "export"` in `next.config.js` — Creek serves as SPA

If you see SSR-related errors but only need static hosting, add to `next.config.js`:

```js
module.exports = {
  output: "export",
};
```

## Unsupported Bindings

Creek currently supports these Cloudflare bindings:
- `d1` — D1 Database (SQLite)
- `kv` — KV Namespace
- `r2` — R2 Object Storage
- `ai` — Workers AI

These bindings are **not yet supported** and will be skipped with a warning:
- `queues` — Message Queues
- `vectorize` — Vector Database
- `hyperdrive` — Database Proxy
- `durable_objects` — Partially supported
- `analytics_engine` — Partially supported

## Invalid Project Name

Project names must match `^[a-z0-9-]+$`:
- Lowercase only
- Letters, numbers, hyphens
- No spaces, underscores, or special characters

**Fix**: Rename in `creek.toml`:
```toml
[project]
name = "my-app"    # Not "My App" or "my_app"
```
