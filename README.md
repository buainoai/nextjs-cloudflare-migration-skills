# Next.js to Cloudflare Workers Migration Guide

A comprehensive guide and toolkit for migrating Next.js 16 applications to Cloudflare Workers using the `@opennextjs/cloudflare` adapter.

## Overview

This repository provides step-by-step guidance, configuration templates, and troubleshooting resources for deploying Next.js 16 applications on Cloudflare Workers. It covers the complete migration workflow from dependency installation to production deployment.

## Features

- **Complete Migration Workflow**: 7-step process from setup to deployment
- **Configuration Templates**: Ready-to-use templates for Wrangler, OpenNext, and Next.js configs
- **Middleware Patterns**: 6 common middleware implementation patterns
- **Troubleshooting Guide**: Solutions for common migration issues
- **Best Practices**: Optimized configurations for Cloudflare Workers environment

## What's Included

### Templates (`templates/`)

- **`middleware.ts.template`**: Next.js middleware template with examples for:
  - Redirects and rewrites
  - API proxying
  - Custom headers
  - Authentication checks
  - Path matching configuration

- **`wrangler.jsonc.template`**: Cloudflare Wrangler configuration with:
  - Required compatibility flags
  - Assets and services bindings
  - R2 bucket configuration (optional)
  - Image optimization setup

- **`open-next.config.ts.template`**: OpenNext adapter configuration with:
  - Basic setup
  - R2 incremental cache option

### References (`references/`)

- **`configuration-reference.md`**: Detailed explanation of all configuration options
- **`troubleshooting.md`**: Common issues and their solutions
- **`middleware-patterns.md`**: 6 practical middleware patterns:
  1. Redirects
  2. Rewrites (Proxying)
  3. Authentication
  4. Custom Headers
  5. A/B Testing
  6. Rate Limiting

## Quick Start

### Prerequisites

- Next.js 16 project
- Node.js v18 or higher
- Cloudflare account
- Wrangler >= 3.99.0

### Basic Migration Steps

1. **Install dependencies**:
   ```bash
   npm install @opennextjs/cloudflare@latest
   npm install --save-dev wrangler@latest
   ```

2. **Copy configuration templates** to your project root:
   ```bash
   # Copy the templates from this repository
   cp templates/wrangler.jsonc.template your-project/wrangler.jsonc
   cp templates/open-next.config.ts.template your-project/open-next.config.ts
   cp templates/middleware.ts.template your-project/middleware.ts  # Optional
   ```

3. **Update `next.config.mjs`**:
   ```javascript
   import { initOpenNextCloudflareForDev } from "@opennextjs/cloudflare";
   initOpenNextCloudflareForDev();
   
   const nextConfig = {
     // Your existing configuration
   };
   
   export default nextConfig;
   ```

4. **Add deployment scripts** to `package.json`:
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

5. **Test locally**:
   ```bash
   npm run preview
   ```

6. **Deploy**:
   ```bash
   npm run deploy
   ```

For detailed instructions, see [GUIDE.md](./GUIDE.md).

## Supported Features

- ✅ Next.js 16 (all minor and patch versions)
- ✅ App Router and Pages Router
- ✅ Server-Side Rendering (SSR)
- ✅ Static Site Generation (SSG)
- ✅ Incremental Static Regeneration (ISR)
- ✅ Middleware
- ✅ Server Actions
- ✅ Image Optimization (via Cloudflare Images)
- ✅ API Routes
- ✅ React Server Components

## Key Differences from Standard Next.js

- Uses Node.js runtime (not Edge runtime)
- Must remove `export const runtime = "edge"` declarations
- Worker size limits: 3 MiB (free) / 10 MiB (paid) compressed
- Test with `preview` mode before deploying (matches production environment)

## Documentation

- [GUIDE.md](./GUIDE.md) - Complete migration guide
- [Configuration Reference](./references/configuration-reference.md)
- [Middleware Patterns](./references/middleware-patterns.md)
- [Troubleshooting Guide](./references/troubleshooting.md)

## Resources

- [OpenNext Cloudflare Documentation](https://opennext.js.org/cloudflare)
- [Cloudflare Workers Documentation](https://developers.cloudflare.com/workers/framework-guides/web-apps/nextjs/)
- [Next.js Middleware Documentation](https://nextjs.org/docs/app/building-your-application/routing/middleware)

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## License

MIT License - feel free to use this guide in your projects.
