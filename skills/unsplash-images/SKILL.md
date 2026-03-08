---
name: unsplash-images
description: "Use this skill when sourcing and integrating Unsplash images into web projects — covers the Unsplash Source API, CDN parameters for sizing and quality, curated category selection, proper attribution, responsive delivery, and React/HTML integration patterns."
license: Complete terms in LICENSE.txt
---

# Unsplash Image Selection & Integration

## When to Use

- Sourcing high-quality photos for hero sections, blog posts, or marketing pages.
- Adding placeholder images during development or prototyping.
- Selecting team photos, testimonial avatars, or background textures.
- Delivering responsive images via Unsplash CDN parameters.
- Building consistent visual themes using Unsplash collections.
- Integrating Unsplash images into React, Next.js, or static HTML projects.

## Unsplash License

Unsplash images are free to use for commercial and non-commercial purposes under the [Unsplash License](https://unsplash.com/license):

- **Allowed:** Use, copy, modify, distribute for any purpose — including commercial.
- **Not required:** Attribution (but strongly encouraged).
- **Not allowed:** Selling unmodified photos, compiling photos for a competing service, using photos with identifiable people in a misleading context.

### Attribution Pattern

Always credit photographers when feasible:

```html
<figcaption>
  Photo by
  <a
    href="https://unsplash.com/@username?utm_source=your_app&utm_medium=referral"
  >
    Photographer Name</a
  >
  on
  <a href="https://unsplash.com?utm_source=your_app&utm_medium=referral"
    >Unsplash</a
  >
</figcaption>
```

Include `utm_source=your_app_name&utm_medium=referral` in attribution links per Unsplash guidelines.

## Unsplash CDN URL Structure

### Base URL Format

```
https://images.unsplash.com/photo-{photo_id}?{parameters}
```

### CDN Parameters

| Parameter | Description          | Example                                     |
| --------- | -------------------- | ------------------------------------------- |
| `w`       | Width in pixels      | `w=800`                                     |
| `h`       | Height in pixels     | `h=600`                                     |
| `fit`     | Resize behavior      | `fit=crop`, `fit=clip`, `fit=max`           |
| `crop`    | Crop focus area      | `crop=faces`, `crop=center`, `crop=entropy` |
| `q`       | Quality (1–100)      | `q=80`                                      |
| `fm`      | Format               | `fm=webp`, `fm=avif`, `fm=jpg`              |
| `auto`    | Auto-format/compress | `auto=format`, `auto=compress`              |
| `dpr`     | Device pixel ratio   | `dpr=2`                                     |
| `blur`    | Blur radius (1–2000) | `blur=50`                                   |

### URL Examples

```
# Full-size, auto-format (CDN picks best)
https://images.unsplash.com/photo-abc123?auto=format&q=80

# 800px wide, cropped, WebP
https://images.unsplash.com/photo-abc123?w=800&fit=crop&fm=webp&q=80

# 400x400 square, face detection crop
https://images.unsplash.com/photo-abc123?w=400&h=400&fit=crop&crop=faces&q=80

# Blur placeholder (tiny + blurred)
https://images.unsplash.com/photo-abc123?w=40&blur=50&q=30

# 2x retina version
https://images.unsplash.com/photo-abc123?w=800&dpr=2&fit=crop&q=75

# AVIF format for maximum compression
https://images.unsplash.com/photo-abc123?w=1200&fm=avif&q=70
```

## Unsplash Source API (Dynamic Images)

The Source API returns random images by keyword, collection, or user — useful for prototyping and dynamic content:

```
# Random image
https://source.unsplash.com/random

# Random by keyword
https://source.unsplash.com/featured/?{keyword}

# Random at specific size
https://source.unsplash.com/random/1600x900

# Random from keyword, at size
https://source.unsplash.com/featured/1600x900/?technology

# Specific photo
https://source.unsplash.com/{photo_id}

# From a collection
https://source.unsplash.com/collection/{collection_id}/1600x900
```

**Note:** The Source API is rate-limited and intended for lightweight use. For production apps, use the [Unsplash API](https://unsplash.com/developers) with proper API keys and trigger download tracking.

## Curated Category Selection

### Recommended Search Terms by Use Case

| Use Case         | Search Terms                                           | Notes                                          |
| ---------------- | ------------------------------------------------------ | ---------------------------------------------- |
| Hero backgrounds | `abstract`, `gradient`, `minimal`, `aerial`, `nature`  | Prefer low-detail images that work behind text |
| Team / people    | `portrait`, `headshot`, `professional`, `team meeting` | Use `crop=faces` for consistent framing        |
| Technology       | `laptop`, `code`, `workspace`, `devices`, `server`     | Avoid dated hardware                           |
| SaaS / product   | `dashboard`, `analytics`, `workspace`, `productivity`  | Use screenshots instead when possible          |
| Blog covers      | `writing`, `notebook`, `creative`, `desk`, `coffee`    | Match the blog topic                           |
| Testimonials     | `portrait`, `headshot`, `professional`                 | Square crop, face-centered                     |
| Backgrounds      | `texture`, `pattern`, `abstract`, `gradient`, `dark`   | Low contrast for text overlay                  |
| Food / lifestyle | `food`, `restaurant`, `cooking`, `healthy`             | Bright, well-lit compositions                  |
| Architecture     | `building`, `interior`, `architecture`, `modern`       | Strong lines, dramatic angles                  |
| Nature           | `landscape`, `mountain`, `ocean`, `forest`, `sky`      | High-resolution, dramatic lighting             |

### Collections for Consistent Themes

Curating a collection ensures visual consistency across a project. Create or use existing Unsplash collections:

```
# Use a collection for themed content
https://source.unsplash.com/collection/1163637/1600x900  (Example: "Workspace" collection)

# Finding collection IDs:
# 1. Browse https://unsplash.com/collections
# 2. The collection ID is in the URL: /collections/{id}/name
```

**Building your own collection:**

1. Create an Unsplash account.
2. Search and curate 20–50 images with consistent visual style.
3. Use the collection ID in Source API URLs for consistent project imagery.

## Integration Patterns

### HTML: Hero Background

```html
<section class="relative h-screen overflow-hidden">
  <!-- Background image with overlay -->
  <img
    src="https://images.unsplash.com/photo-abc123?w=1600&fit=crop&auto=format&q=80"
    srcset="
      https://images.unsplash.com/photo-abc123?w=640&fit=crop&auto=format&q=80   640w,
      https://images.unsplash.com/photo-abc123?w=1024&fit=crop&auto=format&q=80 1024w,
      https://images.unsplash.com/photo-abc123?w=1600&fit=crop&auto=format&q=80 1600w,
      https://images.unsplash.com/photo-abc123?w=2400&fit=crop&auto=format&q=75 2400w
    "
    sizes="100vw"
    alt="Dramatic mountain landscape at sunset"
    class="absolute inset-0 h-full w-full object-cover"
    loading="eager"
    fetchpriority="high"
  />
  <!-- Dark overlay for text readability -->
  <div class="absolute inset-0 bg-black/50"></div>
  <!-- Content -->
  <div class="relative z-10 flex h-full items-center justify-center text-white">
    <h1 class="text-5xl font-bold">Your Headline Here</h1>
  </div>
</section>
```

### HTML: Team Photos Grid

```html
<div class="grid grid-cols-2 gap-4 sm:grid-cols-3 lg:grid-cols-4">
  <figure>
    <img
      src="https://images.unsplash.com/photo-person1?w=300&h=300&fit=crop&crop=faces&auto=format&q=80"
      alt="Alex Rivera, Engineering Lead"
      class="aspect-square w-full rounded-lg object-cover"
      width="300"
      height="300"
      loading="lazy"
    />
    <figcaption class="mt-2 text-sm font-medium">Alex Rivera</figcaption>
    <p class="text-xs text-gray-500">Engineering Lead</p>
  </figure>
  <!-- Repeat for each team member -->
</div>
```

### HTML: Testimonial Avatars

```html
<div class="flex items-center gap-3">
  <img
    src="https://images.unsplash.com/photo-avatar1?w=80&h=80&fit=crop&crop=faces&auto=format&q=80"
    alt="Sarah Chen"
    class="h-10 w-10 rounded-full object-cover"
    width="40"
    height="40"
    loading="lazy"
  />
  <div>
    <p class="text-sm font-semibold">Sarah Chen</p>
    <p class="text-xs text-gray-500">VP Engineering, Acme Corp</p>
  </div>
</div>
```

### HTML: Blog Post Covers

```html
<article class="overflow-hidden rounded-lg border">
  <img
    src="https://images.unsplash.com/photo-blog1?w=800&h=450&fit=crop&auto=format&q=80"
    alt="Modern workspace with dual monitors showing analytics"
    class="aspect-video w-full object-cover"
    width="800"
    height="450"
    loading="lazy"
  />
  <div class="p-6">
    <h2 class="text-lg font-bold">Blog Post Title</h2>
    <p class="mt-2 text-sm text-gray-600">Excerpt text goes here...</p>
  </div>
</article>
```

### React: Unsplash Image Component

```jsx
/**
 * UnsplashImage component with responsive delivery and blur-up placeholder.
 *
 * @param {string} photoId - Unsplash photo ID (e.g., "photo-abc123")
 * @param {string} alt - Alt text (required)
 * @param {number} width - Display width
 * @param {number} height - Display height
 * @param {string} crop - Crop mode: 'faces', 'center', 'entropy'
 * @param {number} quality - Image quality 1–100
 * @param {boolean} eager - If true, load immediately (above-the-fold)
 */
function UnsplashImage({
  photoId,
  alt,
  width = 800,
  height = 600,
  crop = "center",
  quality = 80,
  eager = false,
  className = "",
}) {
  const baseUrl = `https://images.unsplash.com/${photoId}`;

  const params = (w, q = quality) =>
    `?w=${w}&fit=crop&crop=${crop}&auto=format&q=${q}`;

  const placeholderUrl = `${baseUrl}?w=40&blur=50&q=30`;

  const srcSet = [400, 800, 1200, 1600]
    .map((w) => `${baseUrl}${params(w)} ${w}w`)
    .join(", ");

  return (
    <div
      className={`overflow-hidden ${className}`}
      style={{
        backgroundImage: `url(${placeholderUrl})`,
        backgroundSize: "cover",
        backgroundPosition: "center",
      }}
    >
      <img
        src={`${baseUrl}${params(width)}`}
        srcSet={srcSet}
        sizes={`(max-width: 640px) 100vw, (max-width: 1024px) 80vw, ${width}px`}
        alt={alt}
        width={width}
        height={height}
        loading={eager ? "eager" : "lazy"}
        decoding="async"
        className="h-full w-full object-cover"
        style={{ transition: "opacity 0.5s" }}
      />
    </div>
  );
}

// Usage
<UnsplashImage
  photoId="photo-1551434678-e076c223a692"
  alt="Team collaborating at a modern office"
  width={1200}
  height={675}
  crop="center"
  eager
/>;
```

### Next.js: Image with Unsplash

```jsx
import Image from "next/image";

// next.config.js must include Unsplash domain:
// images: { remotePatterns: [{ hostname: 'images.unsplash.com' }] }

function UnsplashHero({ photoId, alt }) {
  return (
    <div className="relative aspect-video w-full">
      <Image
        src={`https://images.unsplash.com/${photoId}?auto=format&fit=crop&q=80`}
        alt={alt}
        fill
        className="object-cover"
        sizes="100vw"
        priority
        placeholder="blur"
        blurDataURL={`https://images.unsplash.com/${photoId}?w=40&blur=50&q=30`}
      />
    </div>
  );
}
```

### Development Placeholders

Use Unsplash for realistic placeholder images during development:

```jsx
// Placeholder utility for development
const PLACEHOLDER_PHOTOS = {
  avatar: 'photo-1472099645785-5658abf4ff4e',
  hero: 'photo-1497366216548-37526070297c',
  product: 'photo-1531297484001-80022131f5a1',
  nature: 'photo-1506744038136-46273834b3fb',
  office: 'photo-1497366811353-6870744d04b2',
  food: 'photo-1504674900247-0877df9cc836',
  tech: 'photo-1518770660439-4636190af475',
};

function devImage(category, width = 800, height = 600) {
  const id = PLACEHOLDER_PHOTOS[category] || PLACEHOLDER_PHOTOS.hero;
  return `https://images.unsplash.com/${id}?w=${width}&h=${height}&fit=crop&auto=format&q=75`;
}

// Usage in development
<img src={devImage('avatar', 100, 100)} alt="Placeholder avatar" />
<img src={devImage('hero', 1600, 900)} alt="Placeholder hero" />
```

## Performance Optimization

### CDN Parameter Best Practices

```
# Production: use auto=format to let the CDN pick the best format
?auto=format&q=80&w=800&fit=crop

# Thumbnails: aggressive compression
?auto=format&q=60&w=300&h=300&fit=crop&crop=entropy

# Retina: use dpr instead of doubling width
?w=800&dpr=2&auto=format&q=70

# Background images behind text: lower quality is fine
?auto=format&q=50&w=1600&fit=crop
```

### Image Loading Priority

| Image Type              | loading | fetchpriority | Notes                    |
| ----------------------- | ------- | ------------- | ------------------------ |
| Hero / LCP image        | `eager` | `high`        | Load immediately         |
| Above-fold secondary    | `eager` | `auto`        | Browser decides priority |
| Below-fold content      | `lazy`  | `auto`        | Load on approach         |
| Decorative / background | `lazy`  | `low`         | Lowest priority          |

### Preloading Key Images

```html
<!-- Preload the hero image for faster LCP -->
<link
  rel="preload"
  as="image"
  href="https://images.unsplash.com/photo-abc123?w=1600&fit=crop&auto=format&q=80"
  imagesrcset="
    https://images.unsplash.com/photo-abc123?w=640&fit=crop&auto=format&q=80 640w,
    https://images.unsplash.com/photo-abc123?w=1600&fit=crop&auto=format&q=80 1600w
  "
  imagesizes="100vw"
/>
```

## Fallback Strategies

### When Unsplash Is Unavailable

```jsx
function ImageWithFallback({ src, fallbackSrc, alt, ...props }) {
  const [imgSrc, setImgSrc] = React.useState(src);
  const [hasError, setHasError] = React.useState(false);

  return (
    <>
      {hasError && !fallbackSrc ?
        <div
          className="flex items-center justify-center bg-gray-100 text-gray-400"
          style={{ width: props.width, height: props.height }}
          role="img"
          aria-label={alt}
        >
          <svg
            className="h-8 w-8"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path
              strokeLinecap="round"
              strokeLinejoin="round"
              strokeWidth={1.5}
              d="M4 16l4.586-4.586a2 2 0 012.828 0L16 16m-2-2l1.586-1.586a2 2 0 012.828 0L20 14m-6-6h.01M6 20h12a2 2 0 002-2V6a2 2 0 00-2-2H6a2 2 0 00-2 2v12a2 2 0 002 2z"
            />
          </svg>
        </div>
      : <img
          src={imgSrc}
          alt={alt}
          onError={() => {
            if (!hasError && fallbackSrc) {
              setImgSrc(fallbackSrc);
            }
            setHasError(true);
          }}
          {...props}
        />
      }
    </>
  );
}

// Usage
<ImageWithFallback
  src="https://images.unsplash.com/photo-abc123?w=800&auto=format&q=80"
  fallbackSrc="/images/default-hero.jpg"
  alt="Hero image"
  width={800}
  height={450}
  className="w-full object-cover"
/>;
```

### CSS-Only Fallback

```css
.hero-bg {
  /* Fallback solid color */
  background-color: #1e293b;
  /* Fallback local image */
  background-image: url("/images/fallback-hero.jpg");
  /* Unsplash — overrides fallback if it loads */
  background-image: url("https://images.unsplash.com/photo-abc123?w=1600&auto=format&q=80");
  background-size: cover;
  background-position: center;
}
```

## Best Practices

1. **Use CDN parameters** — never serve the original full-resolution Unsplash image. Always append `w`, `q`, and `auto=format`.
2. **Always provide alt text** — describe the image content, not "Unsplash photo."
3. **Credit photographers** — it's not required but it's good practice and costs nothing.
4. **Cache aggressively** — Unsplash CDN URLs are highly cacheable. Set long cache headers on your end too.
5. **Use `auto=format`** — let the CDN serve WebP/AVIF to supported browsers automatically.
6. **Pre-select images** — don't use Source API random images in production. Curate specific photos and store their IDs.
7. **Set width and height** — prevent layout shift by always declaring dimensions.
8. **Use `crop=faces`** for people — ensures faces are visible in cropped images.
9. **Use collections** for consistency — curate a set of images with similar style and color palette.
10. **Track downloads** via the API — if using the official Unsplash API, trigger the download endpoint per their guidelines.

## Anti-Patterns

- **Hotlinking without optimization** — using the raw Unsplash URL without CDN resize parameters wastes bandwidth.
- **Using Source API in production** — random images change on each load, breaking visual consistency and caching.
- **No fallback** — if Unsplash is down or the image is removed, the page looks broken.
- **Ignoring attribution** — while not legally required, failing to credit photographers harms the community.
- **Single image size** — serving a 3000px image to a 300px container wastes mobile data.
- **No lazy loading below the fold** — loads every image upfront, slowing initial page render.
- **Using identifiable people inappropriately** — never use portrait photos to imply endorsement or in sensitive contexts.
- **Hardcoding photo URLs everywhere** — centralize photo IDs in a config or CMS for easy updating.
