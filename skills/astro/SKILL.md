---
name: astro
description: >
  Skill for building with the Astro web framework. Use this skill whenever the user
  mentions Astro, .astro files, static site generation (SSG), islands architecture,
  content collections, SSR / on-demand rendering, Astro adapters, Astro routing,
  Astro middleware, Astro API routes, Astro image optimization, Astro view transitions,
  or deploying an Astro project. Also trigger for questions about Astro config,
  integrations, Astro components, layouts, slots, client directives, server islands,
  environment variables in Astro, or any task where the user is working inside an
  Astro project. When in doubt, use this skill — it covers the full Astro surface area.
metadata:
  authors: "Ziad Elnagar"
---

# Astro Usage Guide

> When code examples or API details are needed beyond what's here, fetch
> [docs.astro.build/llms.txt](https://docs.astro.build/llms.txt) to find the right
> sub-doc, or fetch [docs.astro.build/llms-small.txt](https://docs.astro.build/llms-small.txt)
> for the full abridged docs.

**Astro** is the web framework for content-driven websites (blogs, marketing, docs, e-commerce).
It ships **zero JS by default** and uses **Islands Architecture** for selective, opt-in hydration.

## Bundled References

Read these when the user's task goes deep into that area:

| Topic | File |
|---|---|
| Routing, layouts, slots, directives, images, view transitions | `references/routing-and-components.md` |
| SSR, adapters, middleware, API routes, Astro Actions | `references/ssr-and-adapters.md` |
| Content collections, schemas, querying, rendering | `references/content-collections.md` |

---

## Requirements

- **Node.js**: `v18.20.8`, `v20.3.0`, or `v22.0.0+` (v19 and v21 are **not** supported)
- **Package manager**: npm / pnpm / yarn

---

## CLI Quick Reference

| Command | What it does |
|---|---|
| `npm create astro@latest` | Scaffold a new project (interactive wizard) |
| `npx astro dev` | Start dev server → http://localhost:4321 |
| `npx astro build` | Build for production → `dist/` |
| `npx astro preview` | Preview production build locally |
| `npx astro check` | Type-check project (run before deploying) |
| `npx astro add <name>` | Add an official integration |
| `npx astro sync` | Regenerate TS types (run after schema changes) |

```bash
# Scaffold with a template
npm create astro@latest -- --template blog

# Scaffold with integrations pre-installed
npm create astro@latest -- --add react --add tailwind
```

---

## Project Structure

```
my-project/
├── src/
│   ├── pages/          # REQUIRED — file-based routing
│   ├── components/     # Astro/UI components (convention)
│   ├── layouts/        # Layout components (convention)
│   ├── styles/         # CSS/Sass files (convention)
│   ├── content/        # Content collections
│   │   └── config.ts   # Collection schemas
│   └── middleware.ts   # Request middleware
├── public/             # Static assets — copied as-is, not processed
├── astro.config.mjs    # Astro config (recommended)
└── tsconfig.json       # TypeScript config (recommended)
```

`src/pages/` is the only **required** directory.

---

## `astro.config.mjs`

```ts
import { defineConfig } from 'astro/config';
import react from '@astrojs/react';
import tailwind from '@astrojs/tailwind';
import vercel from '@astrojs/vercel';

export default defineConfig({
  // Deployed URL — needed for sitemaps, canonical URLs
  site: 'https://example.com',

  // 'static' (default SSG) | 'server' (full SSR) | 'hybrid' (SSG+opt-in SSR)
  output: 'static',

  // Required when output is 'server' or 'hybrid'
  adapter: vercel(),

  integrations: [react(), tailwind()],

  // Allow remote image domains
  image: {
    domains: ['cdn.example.com'],
  },
});
```

Config format: `.mjs` recommended; also supports `.js`, `.cjs`, `.ts`.

---

## TypeScript (`tsconfig.json`)

```json
{ "extends": "astro/tsconfigs/strict" }
```

Templates: `base` (permissive) · `strict` (recommended) · `strictest` (maximum)

### Import Aliases

```json
{
  "compilerOptions": {
    "paths": {
      "@components/*": ["./src/components/*"],
      "@layouts/*":    ["./src/layouts/*"],
      "@assets/*":     ["./src/assets/*"]
    }
  }
}
```

---

## Astro Components

```astro
---
// src/components/Card.astro
// Frontmatter runs on SERVER only — fetch data, import components here
interface Props {
  title: string;
  body?: string;
}
const { title, body = '' } = Astro.props;
---

<div class="card">
  <h2>{title}</h2>
  {body && <p>{body}</p>}
</div>

<style>
  /* Scoped by default — will not leak out */
  .card { border: 1px solid #ccc; padding: 1rem; }
</style>
```

### Key `Astro.*` Globals

| Global | Description |
|---|---|
| `Astro.props` | Component props |
| `Astro.params` | Dynamic route params |
| `Astro.url` | Current page URL (`URL` object) |
| `Astro.request` | Web `Request` object |
| `Astro.locals` | Data set by middleware |
| `Astro.cookies` | Cookie read/write API |
| `Astro.redirect(url)` | Return redirect (SSR only) |
| `Astro.clientAddress` | Visitor IP (SSR only) |
| `Astro.slots` | `.has(name)`, `.render(name)` |
| `Astro.site` | `config.site` as a `URL` |

→ Full routing, slots, layouts, directives: **`references/routing-and-components.md`**

---

## Routing (Quick)

File path → URL route. See reference for dynamic routes, pagination, catch-alls.

```
src/pages/index.astro       → /
src/pages/about.astro       → /about
src/pages/blog/[slug].astro → /blog/:slug
src/pages/[...path].astro   → /* (catch-all)
src/pages/api/data.ts       → /api/data (endpoint)
```

Custom 404: add `src/pages/404.astro`.

---

## Islands Architecture

By default Astro strips all client JS. Opt in per-component with `client:*` directives:

```astro
---
import Counter from '../components/Counter.tsx';
---

<Counter client:load />           <!-- Hydrate immediately -->
<Counter client:idle />           <!-- Hydrate when browser is idle -->
<Counter client:visible />        <!-- Hydrate when in viewport -->
<Counter client:only="react" />   <!-- Skip SSR; client-render only -->
```

**Server Islands** — stream in personalized/slow server content separately:

```astro
<Avatar server:defer>
  <span slot="fallback">Loading…</span>
</Avatar>
```

Server islands work on any host — no persistent server required.

---

## Content Collections (Quick)

```ts
// src/content/config.ts
import { defineCollection, z } from 'astro:content';

export const collections = {
  blog: defineCollection({
    type: 'content',  // 'content' (.md/.mdx) or 'data' (.json/.yaml)
    schema: z.object({
      title: z.string(),
      pubDate: z.date(),
      draft: z.boolean().default(false),
    }),
  }),
};
```

```astro
---
import { getCollection } from 'astro:content';
const posts = await getCollection('blog', ({ data }) => !data.draft);
---
{posts.map(p => <a href={`/blog/${p.slug}`}>{p.data.title}</a>)}
```

Run `npx astro sync` after changing schemas.

→ Full schema helpers, `reference()`, rendering, dynamic routes: **`references/content-collections.md`**

---

## On-Demand Rendering (SSR)

```ts
// astro.config.mjs
output: 'server',   // or 'hybrid'
adapter: vercel(),  // @astrojs/vercel | netlify | cloudflare | node
```

Per-page opt-in/out:

```astro
---
// In 'hybrid' mode — opt this page into SSR:
export const prerender = false;

// In 'server' mode — opt this page into SSG:
export const prerender = true;
---
```

→ Middleware, API routes, adapters, Astro Actions: **`references/ssr-and-adapters.md`**

---

## Environment Variables

```ts
// Vite built-ins
import.meta.env.PROD    // boolean
import.meta.env.DEV     // boolean
import.meta.env.MODE    // 'development' | 'production'

// From .env
import.meta.env.PUBLIC_API_URL  // PUBLIC_ prefix = safe in browser
import.meta.env.SECRET_KEY      // server-only (no PUBLIC_ prefix)
```

**Type-safe env with `astro:env` (Astro 5+):**

```ts
// astro.config.mjs
import { envField } from 'astro/config';
env: {
  schema: {
    PUBLIC_API_URL: envField.string({ context: 'client', access: 'public' }),
    DB_PASSWORD:    envField.string({ context: 'server', access: 'secret' }),
  },
}
```

```ts
import { PUBLIC_API_URL } from 'astro:env/client';
import { DB_PASSWORD }    from 'astro:env/server';
```

---

## Image Optimization

```astro
---
import { Image, Picture } from 'astro:assets';
import hero from '../assets/hero.jpg';
---
<!-- width, height, alt are required -->
<Image src={hero} width={800} height={600} alt="Hero" />

<!-- Multiple formats for browser choice -->
<Picture src={hero} formats={['avif', 'webp']} alt="Hero" width={800} />
```

Remote images need the domain allow-listed in config (`image.domains`).

---

## Official Integrations

Install: `npx astro add <name>`

| Category | Packages |
|---|---|
| UI Frameworks | `react` `preact` `vue` `svelte` `solid` `lit` |
| SSR Adapters | `vercel` `netlify` `cloudflare` `node` |
| Styling | `tailwind` |
| Content | `mdx` `markdoc` |
| Performance | `partytown` `sitemap` |
| Other | `db` (Astro DB) |

Full list: [astro.build/integrations](https://astro.build/integrations)

---

## Deploy Checklist

1. Set `site` in `astro.config.mjs`
2. `npx astro check` — fix all type errors
3. `npx astro build` — verify `dist/` is non-empty
4. `npx astro preview` — smoke-test the build
5. If SSR: adapter is installed and `output` mode is set

---

## Resources

| | |
|---|---|
| Docs | https://docs.astro.build |
| Config Reference | https://docs.astro.build/en/reference/configuration-reference/ |
| Directives Reference | https://docs.astro.build/en/reference/directives-reference/ |
| API Reference | https://docs.astro.build/en/reference/api-reference/ |
| LLM-friendly docs index | https://docs.astro.build/llms.txt |
| Integrations directory | https://astro.build/integrations |
| GitHub | https://github.com/withastro/astro |