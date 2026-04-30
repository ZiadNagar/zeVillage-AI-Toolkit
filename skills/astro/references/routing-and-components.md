# Routing & Components — Full Reference

## File-Based Routing

| File path                        | URL                     |
| -------------------------------- | ----------------------- |
| `src/pages/index.astro`          | `/`                     |
| `src/pages/about.astro`          | `/about`                |
| `src/pages/blog/index.astro`     | `/blog/`                |
| `src/pages/blog/post-1.md`       | `/blog/post-1`          |
| `src/pages/blog/[slug].astro`    | `/blog/:slug`           |
| `src/pages/docs/[...path].astro` | `/docs/*` (catch-all)   |
| `src/pages/api/hello.ts`         | `/api/hello` (endpoint) |

Supported page formats: `.astro`, `.md`, `.mdx`, `.html`, `.js`, `.ts`

## Dynamic Routes (SSG)

```astro
---
// src/pages/blog/[slug].astro
export async function getStaticPaths() {
  return [
    { params: { slug: 'hello-world' }, props: { title: 'Hello World' } },
    { params: { slug: 'second-post' }, props: { title: 'Second Post' } },
  ];
}
const { title } = Astro.props;
const { slug }  = Astro.params;
---
<h1>{title}</h1>
```

### Pagination with `paginate()`

```astro
---
export async function getStaticPaths({ paginate }) {
  const posts = await getCollection('blog');
  return paginate(posts, { pageSize: 10 });
}
const { page } = Astro.props;
// page.data       → current page items
// page.url.prev   → previous page URL
// page.url.next   → next page URL
// page.total      → total items
// page.currentPage / page.lastPage
---
```

## Rest / Catch-all Routes

```astro
// src/pages/docs/[...slug].astro
// Matches: /docs/, /docs/intro, /docs/a/b/c
const { slug } = Astro.params; // undefined | "intro" | "a/b/c"
```

## Route Priority

When multiple routes could match a URL, Astro uses this priority:
1. Static routes (`/about`)
2. Dynamic routes with named params (`/blog/[slug]`)
3. Rest parameters (`/docs/[...slug]`)
4. `404.astro` for unmatched routes

## `src/pages/404.astro`

Create this file to provide a custom 404 page.

## `Astro.*` Globals — Complete Reference

| Global                         | Type               | Description                                         |
| ------------------------------ | ------------------ | --------------------------------------------------- |
| `Astro.props`                  | `object`           | Props passed to component                           |
| `Astro.request`                | `Request`          | Web API Request object                              |
| `Astro.response`               | `ResponseInit`     | Mutable response headers/status                     |
| `Astro.url`                    | `URL`              | Current page URL                                    |
| `Astro.site`                   | `URL \| undefined` | `config.site` as URL                                |
| `Astro.params`                 | `object`           | Dynamic route params                                |
| `Astro.locals`                 | `object`           | Middleware-set data                                 |
| `Astro.cookies`                | `AstroCookies`     | Cookie read/write API                               |
| `Astro.redirect(url, status?)` | `Response`         | Return a redirect (SSR only)                        |
| `Astro.clientAddress`          | `string`           | Visitor IP (SSR only)                               |
| `Astro.generator`              | `string`           | `"Astro vX.Y.Z"`                                    |
| `Astro.slots`                  | `object`           | `.has(name)`, `.render(name)`                       |
| `Astro.self`                   | component          | Reference to this component                         |
| `Astro.glob(pattern)`          | `Promise<array>`   | Import many files (legacy — prefer `getCollection`) |

## Component Props

```astro
---
// Named props with defaults
interface Props {
  title: string;
  size?: 'sm' | 'md' | 'lg';
}
const { title, size = 'md' } = Astro.props;

// Spread remaining HTML attrs
const { class: className, ...rest } = Astro.props;
---
<div class={className} {...rest}>{title}</div>
```

## Layouts

```astro
---
// src/layouts/Base.astro
const { title } = Astro.props;
---
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>{title}</title>
    <slot name="head" />    <!-- Named slot for extra <head> content -->
  </head>
  <body>
    <slot />                <!-- Default slot -->
    <slot name="footer" />  <!-- Named slot -->
  </body>
</html>
```

Consume in a page:
```astro
---
import Base from '../layouts/Base.astro';
---
<Base title="Home">
  <link slot="head" rel="stylesheet" href="/extra.css" />
  <main>Content here</main>
  <footer slot="footer">Footer here</footer>
</Base>
```

Consume in Markdown (frontmatter):
```md
---
layout: ../layouts/Base.astro
title: My Post
---
Post content here.
```

## Client Directives (Islands)

| Directive                | Hydrates when…                                   |
| ------------------------ | ------------------------------------------------ |
| `client:load`            | Immediately on page load                         |
| `client:idle`            | Browser becomes idle (`requestIdleCallback`)     |
| `client:visible`         | Element enters viewport (`IntersectionObserver`) |
| `client:media="(query)"` | CSS media query matches                          |
| `client:only="react"`    | Skip SSR; client-only render                     |

```astro
<ReactCounter client:load />
<HeavyWidget client:visible />
<MobileMenu client:media="(max-width: 768px)" />
<BrowserOnlyMap client:only="react" />
```

## Server Islands

```astro
<!-- Renders immediately with fallback, streams in personalized content -->
<Avatar server:defer>
  <span slot="fallback">Loading…</span>
</Avatar>
```

Works on any host — no persistent server required.

## Scoped vs Global Styles

```astro
<style>
  /* Scoped — only affects this component */
  p { color: coral; }
</style>

<style is:global>
  /* Global — affects all matching elements */
  * { box-sizing: border-box; }
</style>
```

Import global styles from layouts:
```astro
---
import '../styles/global.css';
---
```

## Image Optimization (`astro:assets`)

```astro
---
import { Image, Picture } from 'astro:assets';
import hero from '../assets/hero.jpg';
---

<!-- width, height, alt required -->
<Image src={hero} width={800} height={600} alt="Hero" />

<!-- Responsive with format choices -->
<Picture src={hero} formats={['avif', 'webp']} alt="Hero" width={800} />

<!-- Remote image: configure domain in astro.config.mjs first -->
<Image src="https://cdn.example.com/photo.jpg" width={400} height={300} alt="…" />
```

Allow remote domains:
```ts
// astro.config.mjs
export default defineConfig({
  image: {
    domains: ['cdn.example.com'],
    remotePatterns: [{ protocol: 'https', hostname: '**.cloudinary.com' }],
  },
});
```

## View Transitions

```astro
---
import { ViewTransitions } from 'astro:transitions';
---
<head>
  <ViewTransitions />
</head>
```

Per-element directives:
```astro
<header transition:persist>…</header>
<img transition:name="hero" src={src} alt="…" />
<div transition:animate="slide">…</div>
```

Animations: `fade` (default), `slide`, `none`, or custom CSS.

## Glob Imports

```astro
---
// import.meta.glob — prefer getCollection() for src/content/
const modules = import.meta.glob('../content/posts/*.md', { eager: true });
const posts = Object.values(modules);
---
```