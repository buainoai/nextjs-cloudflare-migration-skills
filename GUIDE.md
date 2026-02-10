
# Next.js to Cloudflare Workers Migration

Migrate Next.js 16 applications to Cloudflare Workers using the `@opennextjs/cloudflare` adapter.

## When to Use This Guide

Use this guide when:
- Migrating an existing Next.js 16 project to Cloudflare Workers
- Setting up middleware for custom routing, proxying, or request handling
- Configuring Wrangler, OpenNext, and Next.js for Cloudflare deployment
- Troubleshooting migration or deployment issues

## Prerequisites

Before starting migration:
- Next.js 16 project (all minor and patch versions supported)
- Node.js v18 or higher
- Cloudflare account
- Wrangler >= 3.99.0 (critical requirement)

## Migration Workflow

### 1. Install Dependencies

Install required packages:

```bash
npm install @opennextjs/cloudflare@latest
npm install --save-dev wrangler@latest
```

### 2. Create Configuration Files

#### wrangler.jsonc

Use template from `templates/wrangler.jsonc.template`. Key points:
- Set `name` to your project name
- Set `compatibility_date` to current date (YYYY-MM-DD, must be >= 2024-09-23)
- Must include `nodejs_compat` and `global_fetch_strictly_public` flags
- Keep `main` as `.open-next/worker.js` (default)

For detailed configuration options, read `references/configuration-reference.md`.

#### open-next.config.ts

Use template from `templates/open-next.config.ts.template`. Basic setup:

```typescript
import { defineCloudflareConfig } from "@opennextjs/cloudflare";

export default defineCloudflareConfig({});
```

For R2 caching (ISR), uncomment the r2IncrementalCache option in the template.

### 3. Modify next.config.mjs

Add initialization for local development bindings:

```javascript
import { initOpenNextCloudflareForDev } from "@opennextjs/cloudflare";

initOpenNextCloudflareForDev();

// Your existing Next.js config
const nextConfig = { /* ... */ };

export default nextConfig;
```

### 4. Implement Middleware (Optional)

If the project requires custom routing, proxying, or request handling logic:

1. Copy `templates/middleware.ts.template` to project root (or `src/` directory) as `middleware.ts`
2. Customize the middleware function with project-specific logic
3. Configure the `matcher` to specify which paths the middleware should run on

**Common middleware use cases:**
- Redirects and rewrites
- API proxying to external services
- Authentication and authorization
- Custom headers
- A/B testing and feature flags

**Note:** Middleware runs on every request that matches the configured paths, so keep logic lightweight.

### 5. Update package.json

Add deployment scripts:

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "open:build": "opennextjs-cloudflare build",
    "preview": "opennextjs-cloudflare build && opennextjs-cloudflare preview",
    "deploy": "opennextjs-cloudflare build && opennextjs-cloudflare deploy",
    "upload": "opennextjs-cloudflare build && opennextjs-cloudflare upload",
    "cf-typegen": "wrangler types --env-interface CloudflareEnv cloudflare-env.d.ts"
  }
}
```

### 6. Test Locally

Two testing modes:
- `npm run dev` - Next.js dev server (Node.js runtime)
- `npm run preview` - Cloudflare Workers runtime (workerd)

**Always test with `preview` before deploying** as it matches production environment.

### 7. Deploy

Deploy to Cloudflare Workers:

```bash
npm run deploy
```

Or connect Git repository for automatic CI/CD deployment.

## Key Differences from Standard Next.js

- Uses Node.js runtime, not Edge runtime
- Remove all `export const runtime = "edge"` declarations
- `preview` uses workerd runtime, different from `dev` (Node.js)
- Worker size limits: 3 MiB (free) / 10 MiB (paid) compressed
- Middleware is fully supported for routing and request handling

## Troubleshooting

For common issues and solutions, read `references/troubleshooting.md`.

Quick checks:
- Wrangler version >= 3.99.0
- compatibility_date >= 2024-09-23
- nodejs_compat flag enabled
- .open-next directory exists after build
- middleware.ts location correct (root or src/)

## References

- OpenNext Cloudflare Docs: https://opennext.js.org/cloudflare
- Cloudflare Workers Docs: https://developers.cloudflare.com/workers/framework-guides/web-apps/nextjs/
- Next.js Middleware Docs: https://nextjs.org/docs/app/building-your-application/routing/middleware
