---
name: image-assets
description: "Use this skill when finding, optimizing, and integrating images into web projects — covers responsive images, lazy loading, WebP/AVIF optimization, blur-up placeholders, art direction, SVG sprites, favicons, and Open Graph images."
license: Complete terms in LICENSE.txt
---

# Image Assets: Discovery, Optimization & Integration

## When to Use

- Selecting and sourcing images for web pages, apps, or marketing materials.
- Optimizing images for performance (WebP, AVIF, responsive srcset).
- Implementing lazy loading with native or Intersection Observer approaches.
- Building blur-up or LQIP placeholder strategies.
- Managing aspect ratios and preventing layout shift (CLS).
- Using the `<picture>` element for art direction.
- Optimizing SVGs and building SVG sprite systems.
- Generating favicons and Open Graph social media images.

## Image Source Strategy

### Source Selection Criteria

| Criteria                 | Weight   | Notes                          |
| ------------------------ | -------- | ------------------------------ |
| License compatibility    | Critical | Verify commercial use rights   |
| Resolution quality       | High     | Minimum 2x display target      |
| Visual consistency       | High     | Match existing site aesthetic  |
| File size                | Medium   | Prefer sources with CDN params |
| Attribution requirements | Medium   | Some licenses require credit   |

### Free License-Compatible Sources

| Source    | License          | Attribution           | Best For                         |
| --------- | ---------------- | --------------------- | -------------------------------- |
| Unsplash  | Unsplash License | Optional (encouraged) | Photos: landscapes, people, tech |
| Pexels    | Pexels License   | Optional              | Photos and videos                |
| Pixabay   | Pixabay License  | Optional              | Photos, illustrations, vectors   |
| unDraw    | MIT              | Optional              | Flat illustrations               |
| Heroicons | MIT              | Not required          | UI icons                         |
| Lucide    | ISC              | Not required          | UI icons                         |
| SVG Repo  | Various          | Check per asset       | SVG icons and illustrations      |

### Attribution Best Practices

Even when attribution is optional, including it builds trust and supports creators:

```html
<!-- Photo credit pattern -->
<figure>
  <img src="/images/hero.webp" alt="Team collaborating in open office" />
  <figcaption>
    Photo by
    <a href="https://unsplash.com/@photographer">Photographer Name</a> on
    <a href="https://unsplash.com">Unsplash</a>
  </figcaption>
</figure>
```

## Image Optimization

### Format Selection Guide

| Format | Best For                              | Browser Support | Compression              |
| ------ | ------------------------------------- | --------------- | ------------------------ |
| WebP   | Photos and illustrations              | 97%+            | 25–34% smaller than JPEG |
| AVIF   | Photos (highest compression)          | 92%+            | 50% smaller than JPEG    |
| JPEG   | Fallback for older browsers           | 100%            | Baseline universal       |
| PNG    | Transparency, screenshots, text-heavy | 100%            | Lossless                 |
| SVG    | Icons, logos, illustrations           | 100%            | Scalable, tiny file size |

### Responsive Image Sizing

Generate multiple sizes for each image:

```
Source image: 3000 × 2000px

Generate:
  hero-400w.webp    — mobile (small)
  hero-800w.webp    — mobile (large) / tablet
  hero-1200w.webp   — tablet / small desktop
  hero-1600w.webp   — desktop
  hero-2400w.webp   — large desktop / retina
```

### Quality Settings

| Use Case            | WebP Quality | AVIF Quality | Notes                                           |
| ------------------- | ------------ | ------------ | ----------------------------------------------- |
| Hero / full-bleed   | 80–85        | 60–70        | Visible, large images                           |
| Thumbnails / cards  | 70–75        | 50–60        | Smaller display size tolerates more compression |
| Background textures | 60–70        | 40–50        | Subtle, detail not critical                     |
| Product photos      | 85–90        | 70–80        | Detail matters                                  |

### Build Tool Integration

```javascript
// Example: Sharp (Node.js) for image optimization
const sharp = require("sharp");

async function optimizeImage(
  inputPath,
  outputDir,
  widths = [400, 800, 1200, 1600],
) {
  for (const width of widths) {
    // WebP
    await sharp(inputPath)
      .resize(width)
      .webp({ quality: 80 })
      .toFile(`${outputDir}/image-${width}w.webp`);

    // AVIF
    await sharp(inputPath)
      .resize(width)
      .avif({ quality: 65 })
      .toFile(`${outputDir}/image-${width}w.avif`);

    // JPEG fallback
    await sharp(inputPath)
      .resize(width)
      .jpeg({ quality: 80, progressive: true })
      .toFile(`${outputDir}/image-${width}w.jpg`);
  }
}
```

## Responsive Image Markup

### srcset with sizes

```html
<img
  src="/images/hero-800w.jpg"
  srcset="
    /images/hero-400w.webp   400w,
    /images/hero-800w.webp   800w,
    /images/hero-1200w.webp 1200w,
    /images/hero-1600w.webp 1600w,
    /images/hero-2400w.webp 2400w
  "
  sizes="
    (max-width: 640px) 100vw,
    (max-width: 1024px) 80vw,
    60vw
  "
  alt="Team collaborating around a shared dashboard"
  width="1600"
  height="900"
  loading="lazy"
  decoding="async"
/>
```

### Picture Element with Format Selection

```html
<picture>
  <!-- AVIF: best compression -->
  <source
    type="image/avif"
    srcset="
      /images/hero-800w.avif   800w,
      /images/hero-1200w.avif 1200w,
      /images/hero-1600w.avif 1600w
    "
    sizes="(max-width: 1024px) 100vw, 60vw"
  />
  <!-- WebP: wide support -->
  <source
    type="image/webp"
    srcset="
      /images/hero-800w.webp   800w,
      /images/hero-1200w.webp 1200w,
      /images/hero-1600w.webp 1600w
    "
    sizes="(max-width: 1024px) 100vw, 60vw"
  />
  <!-- JPEG fallback -->
  <img
    src="/images/hero-1200w.jpg"
    alt="Team collaborating around a shared dashboard"
    width="1600"
    height="900"
    loading="lazy"
    decoding="async"
  />
</picture>
```

## Lazy Loading

### Native Lazy Loading

```html
<!-- Native lazy loading — simplest approach, good browser support -->
<img
  src="/images/feature.webp"
  alt="Feature screenshot"
  width="800"
  height="450"
  loading="lazy"
  decoding="async"
/>
```

**Rules:**

- Never lazy-load above-the-fold images (hero, logo). Use `loading="eager"` or omit the attribute.
- Always set `width` and `height` to prevent layout shift.
- Add `decoding="async"` for non-critical images.

### Intersection Observer Pattern

```javascript
/**
 * Lazy load images using Intersection Observer.
 * Images use data-src instead of src until they enter the viewport.
 */
function initLazyLoading() {
  const images = document.querySelectorAll("img[data-src]");

  if ("IntersectionObserver" in window) {
    const observer = new IntersectionObserver(
      (entries, obs) => {
        entries.forEach((entry) => {
          if (entry.isIntersecting) {
            const img = entry.target;
            img.src = img.dataset.src;
            if (img.dataset.srcset) img.srcset = img.dataset.srcset;
            img.removeAttribute("data-src");
            img.removeAttribute("data-srcset");
            obs.unobserve(img);
          }
        });
      },
      {
        rootMargin: "200px 0px", // Start loading 200px before viewport
      },
    );

    images.forEach((img) => observer.observe(img));
  } else {
    // Fallback: load all images immediately
    images.forEach((img) => {
      img.src = img.dataset.src;
      if (img.dataset.srcset) img.srcset = img.dataset.srcset;
    });
  }
}

document.addEventListener("DOMContentLoaded", initLazyLoading);
```

```html
<!-- Markup for Intersection Observer lazy loading -->
<img
  data-src="/images/feature.webp"
  data-srcset="/images/feature-400w.webp 400w, /images/feature-800w.webp 800w"
  alt="Feature screenshot"
  width="800"
  height="450"
  src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='800' height='450'%3E%3C/svg%3E"
/>
```

## Placeholder Strategies

### Blur-Up (LQIP)

Generate a tiny (20–40px wide) version of the image, inline it as base64, then transition to the full image:

```html
<div class="image-wrapper" style="position:relative; overflow:hidden;">
  <!-- Tiny blurred placeholder (inlined) -->
  <img
    src="data:image/jpeg;base64,/9j/4AAQSkZJRg..."
    alt=""
    aria-hidden="true"
    class="placeholder"
    style="position:absolute; inset:0; width:100%; height:100%;
           object-fit:cover; filter:blur(20px); transform:scale(1.1);"
  />
  <!-- Full image (lazy loaded) -->
  <img
    data-src="/images/hero-1200w.webp"
    alt="Hero image"
    width="1200"
    height="675"
    class="full-image"
    style="position:relative; width:100%; height:auto; opacity:0;
           transition:opacity 0.5s ease-in-out;"
    onload="this.style.opacity=1; this.previousElementSibling.style.opacity=0;"
  />
</div>
```

### Blur-Up with JavaScript

```javascript
/**
 * Blur-up placeholder technique.
 * Transitions from a tiny blurred image to the full-resolution version.
 */
function initBlurUp() {
  const wrappers = document.querySelectorAll(".blur-up-wrapper");

  const observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          const wrapper = entry.target;
          const fullImg = new Image();
          const src = wrapper.dataset.fullSrc;

          fullImg.onload = () => {
            wrapper.style.backgroundImage = `url(${src})`;
            wrapper.classList.add("loaded");
          };

          fullImg.src = src;
          observer.unobserve(wrapper);
        }
      });
    },
    { rootMargin: "200px" },
  );

  wrappers.forEach((w) => observer.observe(w));
}
```

```css
.blur-up-wrapper {
  background-size: cover;
  background-position: center;
  filter: blur(20px);
  transform: scale(1.1);
  transition:
    filter 0.6s ease,
    transform 0.6s ease;
}
.blur-up-wrapper.loaded {
  filter: blur(0);
  transform: scale(1);
}
```

### Solid Color Placeholder

Use the dominant color extracted from the image:

```html
<div style="background-color: #2d3748; aspect-ratio: 16/9;">
  <img
    src="/images/photo.webp"
    alt="Office workspace"
    loading="lazy"
    style="width:100%; height:100%; object-fit:cover;
           opacity:0; transition:opacity 0.3s;"
    onload="this.style.opacity=1"
  />
</div>
```

### Skeleton / Shimmer Placeholder

```css
.image-skeleton {
  background: linear-gradient(90deg, #e2e8f0 25%, #edf2f7 50%, #e2e8f0 75%);
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite;
  aspect-ratio: 16 / 9;
}

@keyframes shimmer {
  0% {
    background-position: -200% 0;
  }
  100% {
    background-position: 200% 0;
  }
}
```

## Aspect Ratio Management

### Preventing CLS (Cumulative Layout Shift)

Always reserve space for images before they load:

```html
<!-- Method 1: width + height attributes (browser calculates aspect ratio) -->
<img src="/photo.webp" alt="..." width="1600" height="900" loading="lazy" />

<!-- Method 2: CSS aspect-ratio -->
<div style="aspect-ratio: 16/9; overflow:hidden;">
  <img
    src="/photo.webp"
    alt="..."
    style="width:100%;height:100%;object-fit:cover;"
  />
</div>

<!-- Method 3: Padding-bottom hack (legacy support) -->
<div style="position:relative; padding-bottom:56.25%; overflow:hidden;">
  <img
    src="/photo.webp"
    alt="..."
    style="position:absolute;inset:0;width:100%;height:100%;object-fit:cover;"
  />
</div>
```

### Common Aspect Ratios

| Ratio | Decimal | Use Case                      |
| ----- | ------- | ----------------------------- |
| 16:9  | 0.5625  | Hero images, video thumbnails |
| 4:3   | 0.75    | Feature cards, screenshots    |
| 3:2   | 0.6667  | Blog images, photos           |
| 1:1   | 1.0     | Avatars, product thumbnails   |
| 21:9  | 0.4286  | Ultra-wide hero banners       |
| 2:3   | 1.5     | Portrait photos, mobile cards |

## Art Direction with Picture Element

Serve different image crops for different screen sizes:

```html
<picture>
  <!-- Mobile: square crop, tight on subject -->
  <source
    media="(max-width: 640px)"
    srcset="/images/hero-mobile-640w.webp"
    width="640"
    height="640"
  />
  <!-- Tablet: 4:3 crop -->
  <source
    media="(max-width: 1024px)"
    srcset="/images/hero-tablet-1024w.webp"
    width="1024"
    height="768"
  />
  <!-- Desktop: full 16:9 -->
  <img
    src="/images/hero-desktop-1600w.webp"
    alt="Team in conference room"
    width="1600"
    height="900"
    loading="eager"
    fetchpriority="high"
  />
</picture>
```

## SVG Optimization

### SVG Optimization Checklist

1. Run through SVGO or similar to remove metadata, comments, and editor artifacts.
2. Remove unnecessary `<g>` wrappers and empty attributes.
3. Use `viewBox` instead of fixed `width`/`height` for scalability.
4. Convert text to paths only if the font is decorative — otherwise keep text for accessibility.
5. Simplify paths — reduce decimal precision to 1–2 places.
6. Remove unused `<defs>` and `<clipPath>` elements.

### SVG Sprite System

```html
<!-- sprites.svg — hidden SVG containing all icon definitions -->
<svg xmlns="http://www.w3.org/2000/svg" style="display:none;">
  <defs>
    <symbol id="icon-check" viewBox="0 0 24 24">
      <path d="M9 16.17L4.83 12l-1.42 1.41L9 19 21 7l-1.41-1.41z" />
    </symbol>
    <symbol id="icon-arrow" viewBox="0 0 24 24">
      <path d="M12 4l-1.41 1.41L16.17 11H4v2h12.17l-5.58 5.59L12 20l8-8z" />
    </symbol>
    <symbol id="icon-close" viewBox="0 0 24 24">
      <path
        d="M19 6.41L17.59 5 12 10.59 6.41 5 5 6.41 10.59 12 5 17.59 6.41 19 12 13.41 17.59 19 19 17.59 13.41 12z"
      />
    </symbol>
  </defs>
</svg>

<!-- Usage anywhere in the page -->
<svg class="w-5 h-5" aria-hidden="true">
  <use href="/sprites.svg#icon-check" />
</svg>

<svg class="w-5 h-5" aria-hidden="true">
  <use href="/sprites.svg#icon-arrow" />
</svg>
```

### SVG as Component (React)

```jsx
function Icon({ name, className = "w-5 h-5", ...props }) {
  return (
    <svg className={className} aria-hidden="true" {...props}>
      <use href={`/sprites.svg#icon-${name}`} />
    </svg>
  );
}

// Usage
<Icon name="check" className="w-6 h-6 text-green-500" />;
```

## Favicon Generation

### Required Favicon Files

```
favicon.ico          — 32×32 (legacy browsers)
favicon.svg          — Scalable, supports dark mode
apple-touch-icon.png — 180×180 (iOS home screen)
icon-192.png         — 192×192 (Android/PWA)
icon-512.png         — 512×512 (PWA splash screen)
```

### HTML Head Markup

```html
<link rel="icon" href="/favicon.ico" sizes="32x32" />
<link rel="icon" href="/favicon.svg" type="image/svg+xml" />
<link rel="apple-touch-icon" href="/apple-touch-icon.png" />
<link rel="manifest" href="/manifest.webmanifest" />
```

### SVG Favicon with Dark Mode

```xml
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 32 32">
  <style>
    rect { fill: #111827; }
    text { fill: #ffffff; }
    @media (prefers-color-scheme: light) {
      rect { fill: #ffffff; }
      text { fill: #111827; }
    }
  </style>
  <rect width="32" height="32" rx="6" />
  <text x="50%" y="50%" dominant-baseline="central"
        text-anchor="middle" font-size="20" font-weight="bold">Z</text>
</svg>
```

## Open Graph & Social Media Images

### Required Meta Tags

```html
<!-- Open Graph -->
<meta property="og:title" content="Product Name — Tagline" />
<meta
  property="og:description"
  content="Brief benefit-driven description, 60–90 chars."
/>
<meta property="og:image" content="https://example.com/og-image.png" />
<meta property="og:image:width" content="1200" />
<meta property="og:image:height" content="630" />
<meta property="og:url" content="https://example.com/page" />
<meta property="og:type" content="website" />

<!-- Twitter Card -->
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:title" content="Product Name — Tagline" />
<meta name="twitter:description" content="Brief benefit-driven description." />
<meta name="twitter:image" content="https://example.com/og-image.png" />
```

### OG Image Specifications

| Platform   | Recommended Size | Aspect Ratio | Format     |
| ---------- | ---------------- | ------------ | ---------- |
| General OG | 1200 × 630       | 1.91:1       | PNG or JPG |
| Twitter    | 1200 × 628       | 1.91:1       | PNG or JPG |
| LinkedIn   | 1200 × 627       | 1.91:1       | PNG or JPG |
| Facebook   | 1200 × 630       | 1.91:1       | PNG or JPG |

### Dynamic OG Image Pattern (Next.js)

```jsx
// app/og/route.jsx — generates OG images on the fly
import { ImageResponse } from "next/og";

export async function GET(request) {
  const { searchParams } = new URL(request.url);
  const title = searchParams.get("title") || "Default Title";

  return new ImageResponse(
    <div
      style={{
        width: "100%",
        height: "100%",
        display: "flex",
        alignItems: "center",
        justifyContent: "center",
        background: "linear-gradient(135deg, #0f172a, #1e293b)",
        color: "#fff",
        fontSize: 64,
        fontWeight: "bold",
        padding: 80,
        lineHeight: 1.2,
      }}
    >
      {title}
    </div>,
    { width: 1200, height: 630 },
  );
}
```

## Next.js Image Optimization

```jsx
import Image from 'next/image';

// Basic usage
<Image
  src="/images/hero.jpg"
  alt="Hero image"
  width={1600}
  height={900}
  priority           // Above-the-fold: disable lazy loading
  placeholder="blur" // Requires static import or blurDataURL
  quality={80}
/>

// Fill mode (responsive inside container)
<div className="relative aspect-video">
  <Image
    src="/images/feature.jpg"
    alt="Feature screenshot"
    fill
    className="object-cover"
    sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
  />
</div>

// With blur placeholder from URL
<Image
  src="https://images.unsplash.com/photo-xxx"
  alt="Dynamic image"
  width={800}
  height={600}
  placeholder="blur"
  blurDataURL="data:image/jpeg;base64,/9j/4AAQSkZJRg..."
/>
```

## Best Practices

1. **Always include alt text** — descriptive for content images, empty (`alt=""`) for decorative.
2. **Set width and height** on every `<img>` to prevent layout shift.
3. **Use `loading="eager"` for above-the-fold** images and `loading="lazy"` for everything else.
4. **Serve modern formats** (WebP/AVIF) with JPEG/PNG fallbacks via `<picture>`.
5. **Compress aggressively** — most images look fine at 70–80% WebP quality.
6. **Use an image CDN** (Cloudinary, imgix, Vercel Image Optimization) for dynamic resizing.
7. **Generate responsive sizes** — don't serve a 3000px image to a 400px container.
8. **Test with Lighthouse** — check "Properly size images" and "Serve images in modern formats."
9. **Add `fetchpriority="high"`** to the LCP (Largest Contentful Paint) image.
10. **Verify OG images** with the Facebook Sharing Debugger and Twitter Card Validator.

## Anti-Patterns

- **No alt text** — fails accessibility and loses SEO value.
- **Missing width/height** — causes Cumulative Layout Shift, hurting Core Web Vitals.
- **Serving original uploads** — a 5 MB DSLR photo has no place on a web page.
- **Single image size for all devices** — wastes bandwidth on mobile, looks blurry on retina.
- **Lazy loading the hero image** — delays LCP, harming performance scores.
- **Inline SVGs everywhere** — increases HTML size; use sprites or external references for repeated icons.
- **Using PNG for photos** — PNG files are 3–5x larger than WebP for photographic content.
- **Ignoring licensing** — using copyrighted images without permission creates legal risk.
- **No placeholder strategy** — images that pop in abruptly create a jarring user experience.
