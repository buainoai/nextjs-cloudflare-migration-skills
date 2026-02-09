# Troubleshooting Guide

## Common Issues

### Wrangler Version Error

**Symptom**: Deployment fails with version error

**Solution**: Ensure Wrangler >= 3.99.0
```bash
npm install --save-dev wrangler@latest
```

### Compatibility Date Error

**Symptom**: Error about compatibility_date

**Solution**: Set compatibility_date to 2024-09-23 or later in wrangler.jsonc

### Missing nodejs_compat Flag

**Symptom**: Node.js APIs not available

**Solution**: Add to wrangler.jsonc:
```jsonc
"compatibility_flags": ["nodejs_compat", "global_fetch_strictly_public"]
```

### Worker Size Limit Exceeded

**Symptom**: Worker exceeds 3 MiB (free) or 10 MiB (paid)

**Solution**: 
- Check compressed size (shown by wrangler)
- Only compressed size counts toward limit
- Optimize dependencies and bundle size

### Middleware Not Working

**Symptom**: middleware.ts changes not reflected

**Solution**: 
1. Verify middleware.ts is in project root or src/ directory
2. Check matcher configuration in middleware.ts
3. Rebuild with `npm run open:build`
4. Test with `npm run preview` (not just `npm run dev`)

### Middleware Runs on Every Request

**Symptom**: Performance issues, middleware executing too often

**Solution**: Configure matcher to limit which paths trigger middleware:
```typescript
export const config = {
  matcher: [
    '/api/:path*',  // Only API routes
    '/protected/:path*',  // Only protected routes
  ],
};
```

### Local Development Bindings Not Available

**Symptom**: Cannot access bindings in `next dev`

**Solution**: Add to next.config.mjs:
```javascript
import { initOpenNextCloudflareForDev } from "@opennextjs/cloudflare";
initOpenNextCloudflareForDev();
```

### Preview vs Dev Differences

**Issue**: App works in dev but not in preview

**Explanation**: 
- `next dev` uses Node.js runtime
- `preview` uses workerd (Cloudflare Workers runtime)
- Always test with `npm run preview` before deploying

### Edge Runtime Not Supported

**Symptom**: Error with `export const runtime = "edge"`

**Solution**: Remove all `export const runtime = "edge"` declarations. Use Node.js runtime instead.

### Build Output Not Found

**Symptom**: Cannot find .open-next directory

**Solution**: 
1. Run `npm run open:build` first
2. Add `.open-next` to .gitignore
3. Do not commit build output

## Getting Help

- OpenNext Docs: https://opennext.js.org/cloudflare
- Cloudflare Workers Docs: https://developers.cloudflare.com/workers
- GitHub Issues: https://github.com/opennextjs/opennextjs-cloudflare/issues
