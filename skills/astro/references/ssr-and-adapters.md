# SSR & On-Demand Rendering — Full Reference

## Output Modes

| Mode                 | Description                                                |
| -------------------- | ---------------------------------------------------------- |
| `'static'` (default) | Full SSG — all pages pre-rendered at build time            |
| `'server'`           | Full SSR — all pages rendered on demand (requires adapter) |
| `'hybrid'`           | SSG by default; opt specific pages into SSR                |

```ts
// astro.config.mjs
import { defineConfig } from 'astro/config';
import node from '@astrojs/node';

export default defineConfig({
  output: 'server',
  adapter: node({ mode: 'standalone' }),
});
```

## Official Adapters

| Host       | Package               | Install                    |
| ---------- | --------------------- | -------------------------- |
| Vercel     | `@astrojs/vercel`     | `npx astro add vercel`     |
| Netlify    | `@astrojs/netlify`    | `npx astro add netlify`    |
| Cloudflare | `@astrojs/cloudflare` | `npx astro add cloudflare` |
| Node.js    | `@astrojs/node`       | `npx astro add node`       |

## Per-Page Prerendering Control

In `output: 'hybrid'` — pages are SSG by default, opt into SSR:

```astro
---
export const prerender = false; // This page is SSR
---
```

In `output: 'server'` — pages are SSR by default, opt into SSG:

```astro
---
export const prerender = true; // This page is pre-rendered
---
```

## SSR-Only APIs

These are only available in SSR / non-prerendered contexts:

```astro
---
// Reading request headers and cookies
const token = Astro.request.headers.get('Authorization');
const session = Astro.cookies.get('session')?.value;

// Setting cookies
Astro.cookies.set('theme', 'dark', { httpOnly: true });

// Redirecting
if (!session) return Astro.redirect('/login');

// Client IP (forwarded by adapter)
const ip = Astro.clientAddress;

// Reading Locals (set by middleware)
const user = Astro.locals.user;
---
```

## Middleware (server-side)

```ts
// src/middleware.ts
import { defineMiddleware, sequence } from 'astro:middleware';

const auth = defineMiddleware(async ({ cookies, locals, redirect }, next) => {
  const session = cookies.get('session')?.value;
  locals.user = session ? await getUserFromSession(session) : null;
  return next();
});

const logging = defineMiddleware(async ({ request }, next) => {
  console.log(`[${new Date().toISOString()}] ${request.method} ${request.url}`);
  return next();
});

// Run middleware in sequence
export const onRequest = sequence(auth, logging);
```

## API Routes / Server Endpoints

```ts
// src/pages/api/posts.ts
import type { APIRoute } from 'astro';

export const GET: APIRoute = async ({ request, locals, params }) => {
  const posts = await db.query('SELECT * FROM posts');
  return new Response(JSON.stringify(posts), {
    headers: { 'Content-Type': 'application/json' },
  });
};

export const POST: APIRoute = async ({ request }) => {
  const data = await request.json();
  // process data...
  return new Response(JSON.stringify({ ok: true }), { status: 201 });
};

// For static output with dynamic params, still need getStaticPaths
export async function getStaticPaths() { /* ... */ }
```

Supported methods: `GET`, `POST`, `PUT`, `PATCH`, `DELETE`, `ALL`

## Astro Actions (Astro 4.15+)

Type-safe form and data mutations with automatic validation:

```ts
// src/actions/index.ts
import { defineAction } from 'astro:actions';
import { z } from 'astro:schema';

export const server = {
  contact: defineAction({
    input: z.object({ name: z.string(), email: z.string().email() }),
    handler: async ({ name, email }) => {
      await sendEmail({ name, email });
      return { success: true };
    },
  }),
};
```

```astro
---
import { actions } from 'astro:actions';
const result = await Astro.callAction(actions.contact, Astro.request);
---
```

## Environment Variables (Server)

```ts
// Server-only (no PUBLIC_ prefix):
const dbUrl = import.meta.env.DATABASE_URL;

// Type-safe with astro:env (Astro 5+):
import { DB_PASSWORD } from 'astro:env/server';
```
