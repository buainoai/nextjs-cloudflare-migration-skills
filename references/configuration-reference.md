# Configuration Reference

## wrangler.jsonc

### Required Fields

- `main`: Entry point for the Worker
  - Default: `.open-next/worker.js` (do not change unless using advanced custom Worker setup)
  
- `name`: Worker name (must match `services.service`)

- `compatibility_date`: Must be `2024-09-23` or later

- `compatibility_flags`: Must include:
  - `nodejs_compat` - Enable Node.js API
  - `global_fetch_strictly_public` - Allow fetch URLs in app

### Assets Configuration

```jsonc
"assets": {
  "directory": ".open-next/assets",
  "binding": "ASSETS"
}
```

Do not modify unless customizing build output.

### Services Configuration

```jsonc
"services": [
  {
    "binding": "WORKER_SELF_REFERENCE",
    "service": "my-app"  // Must match "name"
  }
]
```

### R2 Buckets (Optional)

Enable for ISR caching:

```jsonc
"r2_buckets": [
  {
    "binding": "NEXT_INC_CACHE_R2_BUCKET",
    "bucket_name": "my-cache-bucket"
  }
]
```

### Images Binding

```jsonc
"images": {
  "binding": "IMAGES"
}
```

Required for Next.js image optimization via Cloudflare Images.

## open-next.config.ts

### Basic Configuration

```typescript
import { defineCloudflareConfig } from "@opennextjs/cloudflare";

export default defineCloudflareConfig({});
```

### With R2 Incremental Cache

```typescript
import { defineCloudflareConfig } from "@opennextjs/cloudflare";
import r2IncrementalCache from "@opennextjs/cloudflare/overrides/incremental-cache/r2-incremental-cache";

export default defineCloudflareConfig({
  incrementalCache: r2IncrementalCache,
});
```

## next.config.mjs

Add initialization for local development:

```javascript
import { initOpenNextCloudflareForDev } from "@opennextjs/cloudflare";

initOpenNextCloudflareForDev();

const nextConfig = {
  // Your Next.js configuration
};

export default nextConfig;
```

## package.json Scripts

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

## .dev.vars (Optional)

For local development environment variables:

```
NEXTJS_ENV=development
```

Defaults to "production" if not set.
