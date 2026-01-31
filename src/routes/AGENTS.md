# src/routes

## OVERVIEW
HTTP API endpoints for worker: /api/*, /_admin/*, /debug/*, /cdp/*, /sandbox-health.

## WHERE TO LOOK
- `api.ts` - device/gateway/process management (start here for new endpoints)
- `cdp.ts` - Browser Rendering CDP shim (avoid unless modifying CDP integration)
- `debug.ts` - troubleshooting endpoints (conditionally enabled)
- `admin-ui.ts` - React SPA mounting
- `public.ts` - health/status endpoints

## KEY PATTERNS
- Use Hono's `c.json()`, `c.html()` for responses; avoid raw Response objects
- Debug routes gated by `env.DEBUG_ROUTES === 'true'` guard
- API routes use `auth.middleware`; admin routes serve static assets
- CDP shim maps browser CDP ↔ worker WebSocket (complex - treat as opaque)
- Thin handlers: extract business logic to gateway/ or routes/ modules

## ANTI-PATTERNS
- DO NOT put auth logic in individual routes (use middleware)
- DO NOT embed CLI command strings directly in handlers (extract to gateway/)
- DO NOT skip env var guards on debug endpoints
- DO NOT mutate request bodies; read-only access
