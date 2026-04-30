# Content Collections — Full Reference

The recommended way to manage typed, validated local content in `src/content/`.

## Define Schema (`src/content/config.ts`)

```ts
import { defineCollection, z } from 'astro:content';

const blog = defineCollection({
  type: 'content',   // 'content' for .md/.mdx, 'data' for .json/.yaml
  schema: z.object({
    title: z.string(),
    pubDate: z.date(),
    tags: z.array(z.string()).optional(),
    draft: z.boolean().default(false),
    image: z.object({ src: z.string(), alt: z.string() }).optional(),
  }),
});

const authors = defineCollection({
  type: 'data',
  schema: z.object({
    name: z.string(),
    bio: z.string().optional(),
  }),
});

export const collections = { blog, authors };
```

**Run `npx astro sync` after changing schemas** to regenerate TypeScript types.

## Querying Collections

```ts
import { getCollection, getEntry, getEntries } from 'astro:content';

// All entries, with optional filter
const posts = await getCollection('blog', ({ data }) => !data.draft);

// Single entry by slug
const post = await getEntry('blog', 'my-first-post');

// Multiple entries by reference
const relatedPosts = await getEntries(post.data.relatedPosts);
```

## Rendering Content

```astro
---
const post = await getEntry('blog', slug);
const { Content, headings, remarkPluginFrontmatter } = await post.render();
---
<h1>{post.data.title}</h1>
<Content />
```

## Dynamic Routes from Collections (SSG)

```astro
---
// src/pages/blog/[...slug].astro
import { getCollection } from 'astro:content';

export async function getStaticPaths() {
  const posts = await getCollection('blog');
  return posts.map(post => ({
    params: { slug: post.slug },
    props: { post },
  }));
}

const { post } = Astro.props;
const { Content } = await post.render();
---
<h1>{post.data.title}</h1>
<Content />
```

## Schema Helpers

Astro exports schema helpers for common content patterns:

```ts
import { defineCollection, z, reference } from 'astro:content';

const blog = defineCollection({
  schema: ({ image }) => z.object({    // image() validates local image paths
    cover: image().optional(),
    author: reference('authors'),       // reference() links to another collection
    pubDate: z.coerce.date(),           // coerce string dates from frontmatter
  }),
});
```

## Collection File Structure

```
src/content/
├── config.ts          # Schema definitions (required)
├── blog/
│   ├── post-1.md
│   ├── post-2.mdx
│   └── post-3/
│       ├── index.md
│       └── image.png  # Co-located assets
└── authors/
    └── jane.json      # Data collection
```

## Entry Properties

| Property           | Type      | Description                                  |
| ------------------ | --------- | -------------------------------------------- |
| `entry.id`         | `string`  | Unique ID (file path relative to collection) |
| `entry.slug`       | `string`  | URL-safe slug (content collections only)     |
| `entry.data`       | `object`  | Validated frontmatter/data                   |
| `entry.body`       | `string`  | Raw content body                             |
| `entry.collection` | `string`  | Collection name                              |
| `entry.render()`   | `Promise` | Returns `{ Content, headings }`              |