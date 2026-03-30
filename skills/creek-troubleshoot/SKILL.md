---
name: creek-troubleshoot
description: Diagnose and fix common Creek deployment issues. Use when the user
  encounters errors with creek deploy, creek dev, creek login, build failures,
  or asks about troubleshooting Creek problems.
license: Apache-2.0
metadata:
  author: solcreek
  version: "1.0"
---

# Troubleshoot Creek

## Quick Diagnostic Steps

1. **Check authentication**: `creek whoami`
2. **Check project config**: Verify `creek.toml` exists or a supported framework is detected
3. **Check deployment status**: `creek status` or `creek deployments`
4. **Use JSON output for details**: Add `--json` to any command

## Common Errors

### Authentication

| Error | Cause | Fix |
|-------|-------|-----|
| "Not authenticated. Run `creek login` first." | No token found | Run `creek login` |
| "Invalid API key" | Token expired or revoked | Run `creek login` to re-authenticate |

**CI/CD**: Set `CREEK_TOKEN` environment variable instead of interactive login.

```bash
CREEK_TOKEN=ck_... creek deploy --yes
```

### Project Detection

| Error | Cause | Fix |
|-------|-------|-----|
| "No creek.toml found" | No config in current directory | Run `creek init` or `cd` to project root |
| "No supported project found in repo" | Monorepo without path specified | Use `creek deploy --path packages/app` |
| "No project found in this directory" | No package.json or index.html | Deploy a specific build output: `creek deploy ./dist` |

### Build Failures

**Framework not detected**: Creek checks `package.json` dependencies. Ensure your framework is listed as a dependency (not just devDependency for some frameworks).

**Build command fails**: Override in `creek.toml`:
```toml
[build]
command = "npm run build"
output = "dist"
```

**Wrong output directory**: Check your framework's build output matches `[build] output` in creek.toml. See [COMMON-ERRORS.md](references/COMMON-ERRORS.md) for framework-specific defaults.

### Deployment

| Error | Cause | Fix |
|-------|-------|-----|
| "Sandbox not found. It may have expired." | Sandbox older than 60 min | Redeploy — sandboxes are temporary |
| "Binding 'X' is not yet supported" | Unsupported Cloudflare resource | Binding is skipped; use supported bindings (d1, kv, r2, ai) |
| "Project 'X' does not exist. Create it?" | First deploy for this project | Confirm to auto-create |

### Custom Domains

| Error | Cause | Fix |
|-------|-------|-----|
| Domain stuck on "pending" | DNS not configured | Add CNAME: `your-domain → cname.creek.dev` |
| Domain status "failed" | Wrong DNS record | Verify CNAME record, then `creek domains activate` |
| SSL not provisioning | DNS propagation delay | Wait a few minutes, then retry `creek domains activate` |

### Dev Server

| Error | Cause | Fix |
|-------|-------|-----|
| Port already in use | Another process on port 3000 | Use `creek dev --port 3001` |
| Stale local state | Cached data from previous runs | Use `creek dev --reset` |

## Environment Variables

```bash
# Override API endpoints (advanced)
CREEK_API_URL=https://api.creek.dev
CREEK_SANDBOX_API_URL=https://sandbox-api.creek.dev

# Authentication
CREEK_TOKEN=ck_...
```

## Getting Help

- All commands support `--json` for structured error output
- Use `creek status [DEPLOYMENT_ID]` to check deployment progress
- Use `creek deployments --project my-app` to list recent deploys
