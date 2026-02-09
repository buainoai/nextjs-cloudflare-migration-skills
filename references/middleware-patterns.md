# Middleware Patterns

Common patterns for implementing Next.js middleware in Cloudflare Workers deployments.

## Basic Middleware Structure

```typescript
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  // Your logic here
  return NextResponse.next();
}

export const config = {
  matcher: [/* paths */],
};
```

## Pattern 1: Redirects

```typescript
export function middleware(request: NextRequest) {
  // Redirect old paths to new paths
  if (request.nextUrl.pathname === '/old-page') {
    return NextResponse.redirect(new URL('/new-page', request.url));
  }
  
  // Redirect based on conditions
  const country = request.geo?.country;
  if (country === 'CN' && request.nextUrl.pathname === '/') {
    return NextResponse.redirect(new URL('/cn', request.url));
  }
  
  return NextResponse.next();
}
```

## Pattern 2: Rewrites (Proxying)

```typescript
export function middleware(request: NextRequest) {
  // Rewrite API requests to external service
  if (request.nextUrl.pathname.startsWith('/api/external')) {
    const externalUrl = new URL(
      request.nextUrl.pathname.replace('/api/external', ''),
      'https://api.example.com'
    );
    return NextResponse.rewrite(externalUrl);
  }
  
  return NextResponse.next();
}
```

## Pattern 3: Authentication

```typescript
export function middleware(request: NextRequest) {
  const token = request.cookies.get('auth-token');
  
  // Protect routes
  if (request.nextUrl.pathname.startsWith('/dashboard')) {
    if (!token) {
      return NextResponse.redirect(new URL('/login', request.url));
    }
  }
  
  return NextResponse.next();
}

export const config = {
  matcher: ['/dashboard/:path*'],
};
```

## Pattern 4: Custom Headers

```typescript
export function middleware(request: NextRequest) {
  const response = NextResponse.next();
  
  // Add security headers
  response.headers.set('X-Frame-Options', 'DENY');
  response.headers.set('X-Content-Type-Options', 'nosniff');
  response.headers.set('Referrer-Policy', 'strict-origin-when-cross-origin');
  
  // Add custom headers
  response.headers.set('X-Custom-Header', 'value');
  
  return response;
}
```

## Pattern 5: A/B Testing

```typescript
export function middleware(request: NextRequest) {
  // Check for existing variant
  let variant = request.cookies.get('ab-test-variant')?.value;
  
  if (!variant) {
    // Assign random variant
    variant = Math.random() < 0.5 ? 'A' : 'B';
    const response = NextResponse.next();
    response.cookies.set('ab-test-variant', variant);
    return response;
  }
  
  // Route to variant-specific page
  if (variant === 'B' && request.nextUrl.pathname === '/') {
    return NextResponse.rewrite(new URL('/variant-b', request.url));
  }
  
  return NextResponse.next();
}
```

## Pattern 6: Rate Limiting (with KV)

```typescript
export function middleware(request: NextRequest) {
  // Note: Access to KV requires proper binding setup
  const ip = request.ip || 'unknown';
  const key = `rate-limit:${ip}`;
  
  // Check rate limit (implementation depends on KV binding)
  // This is a simplified example
  
  return NextResponse.next();
}
```

## Matcher Configuration

### Match all except static files

```typescript
export const config = {
  matcher: [
    '/((?!_next/static|_next/image|favicon.ico).*)',
  ],
};
```

### Match specific paths

```typescript
export const config = {
  matcher: [
    '/api/:path*',
    '/dashboard/:path*',
  ],
};
```

### Match with regex

```typescript
export const config = {
  matcher: [
    '/(api|dashboard)/:path*',
  ],
};
```

## Best Practices

1. **Keep middleware lightweight** - It runs on every matched request
2. **Use specific matchers** - Avoid running on static assets
3. **Handle errors gracefully** - Always return a response
4. **Test with preview** - Middleware behavior may differ between dev and production
5. **Avoid heavy computation** - Offload to API routes if needed
6. **Use cookies wisely** - Be mindful of cookie size limits
