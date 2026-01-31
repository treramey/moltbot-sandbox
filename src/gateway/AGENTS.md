# Gateway Module

## OVERVIEW
Manages Moltbot gateway lifecycle inside Cloudflare Sandbox container: process lifecycle, env var mapping, R2 mounting, and backup sync.

## WHERE TO LOOK
- `src/gateway/process.ts` - Find, start, and wait for gateway process
- `src/gateway/env.ts` - Build env vars passed to container
- `src/gateway/r2.ts` - Mount R2 bucket via s3fs
- `src/gateway/sync.ts` - Sync data to R2 backup
- `src/gateway/utils.ts` - `waitForProcess()` helper

## KEY PATTERNS
- **Container DO**: Access via `env.SANDBOX.get(ctx)` to spawn processes
- **CLI name**: Always use `openclaw` (not moltbot) with `--url ws://localhost:18789`
- **Env mapping**: Worker env → container env in `buildEnvVars()` (src/gateway/env.ts:45)
- **R2 mount**: `/data/moltbot` IS the R2 bucket—never delete contents
- **rsync**: Use `rsync -r --no-times` (s3fs rejects timestamp updates)
- **Mount check**: `mount | grep s3fs` instead of relying on API errors
- **Success detection**: CLI outputs "Approved" (case-insensitive check)
- **Process wait**: 10-15s overhead per CLI command due to WebSocket

## ANTI-PATTERNS
- **NEVER** `rm -rf /data/moltbot/*` — deletes R2 data
- **NEVER** check `proc.status === 'completed'` for success—check expected output/files
- **NEVER** use `rsync -a` — fails with "Input/output error" on s3fs
- **NEVER** rely on error messages to detect "already mounted"—check mount directly
