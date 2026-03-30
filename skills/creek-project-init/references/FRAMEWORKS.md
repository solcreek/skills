# Framework Reference

## Default Build Outputs

Each framework has a default output directory used when `[build] output` is not set in `creek.toml`:

| Framework | Output Directory |
|-----------|-----------------|
| `nextjs` | `.open-next/` |
| `react-router` | `build/client/` |
| `sveltekit` | `.svelte-kit/output/client/` |
| `solidstart` | `.output/public/` |
| `nuxt` | `.output/public/` |
| `tanstack-start` | `dist/` |
| `vite-react` | `dist/` |
| `vite-vue` | `dist/` |
| `vite-svelte` | `dist/` |
| `vite-solid` | `dist/` |
| (static) | `dist/` |

## SSR Notes

- **Next.js**: Creek auto-installs `@opennextjs/cloudflare` for SSR. If `next.config` has `output: "export"`, it deploys as static.
- **SvelteKit / Nuxt / SolidStart**: Server entry is bundled as a Cloudflare Worker automatically.
- **SPA frameworks** (Vite-based): No server component — static assets only.

## Worker Mode

If `creek.toml` specifies a `[build] worker` entry point, the project deploys as a full Cloudflare Worker with access to bindings (D1, KV, R2, AI).

```toml
[build]
worker = "worker/index.ts"
```
