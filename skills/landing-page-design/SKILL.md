---
name: landing-page-design
description: "Use this skill when designing and building high-conversion landing pages — hero sections, social proof, feature grids, CTAs, FAQ accordions, responsive layout, copywriting patterns, and performance optimization with Tailwind CSS."
license: Complete terms in LICENSE.txt
---

# Landing Page Design

## When to Use

- Building a marketing or product landing page optimized for conversions.
- Designing hero sections, feature showcases, testimonial blocks, or pricing CTAs.
- Structuring above-the-fold content for maximum impact.
- Writing headline and CTA copy that drives action.
- Creating responsive, accessible landing pages with Tailwind CSS.

## Page Structure

A high-conversion landing page follows a proven information hierarchy:

1. **Hero Section** — headline, subheadline, primary CTA, supporting visual.
2. **Social Proof Bar** — logos, metrics, or a short testimonial.
3. **Features / Benefits** — 3–6 key value propositions with icons or images.
4. **Deep-Dive Section** — detailed walkthrough or demo of the core product.
5. **Testimonials / Case Studies** — real quotes, photos, company names.
6. **Pricing or Secondary CTA** — clear next step.
7. **FAQ** — address objections before they arise.
8. **Footer** — secondary navigation, legal links, final CTA.

## Hero Section Patterns

### Centered Hero

```html
<section class="relative overflow-hidden bg-white dark:bg-gray-950">
  <div class="mx-auto max-w-4xl px-6 py-24 text-center sm:py-32 lg:py-40">
    <!-- Eyebrow -->
    <p class="text-sm font-semibold tracking-wider text-indigo-600 uppercase">
      Now available
    </p>

    <!-- Headline -->
    <h1
      class="mt-4 text-4xl font-bold tracking-tight text-gray-900 dark:text-white sm:text-6xl lg:text-7xl"
    >
      Ship faster without<br class="hidden sm:inline" />
      breaking things
    </h1>

    <!-- Subheadline -->
    <p
      class="mx-auto mt-6 max-w-2xl text-lg leading-relaxed text-gray-600 dark:text-gray-400"
    >
      The deployment platform that gives your team confidence. Push to
      production in minutes, roll back in seconds.
    </p>

    <!-- CTA Group -->
    <div
      class="mt-10 flex flex-col items-center gap-4 sm:flex-row sm:justify-center"
    >
      <a
        href="#"
        class="inline-flex items-center rounded-full bg-indigo-600 px-8 py-3.5 text-sm font-semibold text-white shadow-lg hover:bg-indigo-500 focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600 transition-colors"
      >
        Start free trial
      </a>
      <a
        href="#"
        class="inline-flex items-center gap-2 text-sm font-semibold text-gray-700 dark:text-gray-300 hover:text-indigo-600 transition-colors"
      >
        Watch demo <span aria-hidden="true">&rarr;</span>
      </a>
    </div>
  </div>
</section>
```

### Split Hero (Image + Text)

```html
<section class="bg-white dark:bg-gray-950">
  <div
    class="mx-auto grid max-w-7xl items-center gap-12 px-6 py-20 lg:grid-cols-2 lg:py-32"
  >
    <!-- Text Column -->
    <div>
      <h1
        class="text-4xl font-bold tracking-tight text-gray-900 dark:text-white sm:text-5xl lg:text-6xl"
      >
        Design to code,<br />
        in one click
      </h1>
      <p class="mt-6 text-lg leading-relaxed text-gray-600 dark:text-gray-400">
        Transform your design files into production-ready components. No more
        pixel-pushing handoffs.
      </p>
      <div class="mt-8 flex flex-wrap gap-4">
        <a
          href="#"
          class="rounded-lg bg-gray-900 px-6 py-3 text-sm font-semibold text-white hover:bg-gray-800 transition-colors dark:bg-white dark:text-gray-900 dark:hover:bg-gray-200"
        >
          Get started
        </a>
        <a
          href="#"
          class="rounded-lg border border-gray-300 px-6 py-3 text-sm font-semibold text-gray-700 hover:bg-gray-50 transition-colors dark:border-gray-700 dark:text-gray-300 dark:hover:bg-gray-900"
        >
          Learn more
        </a>
      </div>
    </div>

    <!-- Image Column -->
    <div class="relative">
      <img
        src="/hero-screenshot.png"
        alt="Product dashboard screenshot"
        class="rounded-2xl shadow-2xl ring-1 ring-gray-200 dark:ring-gray-800"
        width="800"
        height="600"
        loading="eager"
      />
    </div>
  </div>
</section>
```

## Social Proof Bar

```html
<section
  class="border-y border-gray-200 bg-gray-50 dark:border-gray-800 dark:bg-gray-900"
>
  <div class="mx-auto max-w-7xl px-6 py-8">
    <p class="text-center text-sm font-medium text-gray-500 dark:text-gray-400">
      Trusted by 2,000+ teams worldwide
    </p>
    <div
      class="mt-6 flex flex-wrap items-center justify-center gap-x-12 gap-y-6"
    >
      <!-- Replace with client logos -->
      <img
        src="/logos/company-a.svg"
        alt="Company A"
        class="h-8 opacity-60 grayscale hover:opacity-100 hover:grayscale-0 transition"
      />
      <img
        src="/logos/company-b.svg"
        alt="Company B"
        class="h-8 opacity-60 grayscale hover:opacity-100 hover:grayscale-0 transition"
      />
      <img
        src="/logos/company-c.svg"
        alt="Company C"
        class="h-8 opacity-60 grayscale hover:opacity-100 hover:grayscale-0 transition"
      />
      <img
        src="/logos/company-d.svg"
        alt="Company D"
        class="h-8 opacity-60 grayscale hover:opacity-100 hover:grayscale-0 transition"
      />
      <img
        src="/logos/company-e.svg"
        alt="Company E"
        class="h-8 opacity-60 grayscale hover:opacity-100 hover:grayscale-0 transition"
      />
    </div>
  </div>
</section>
```

## Feature Grid

### Three-Column Icon Grid

```html
<section class="bg-white py-20 dark:bg-gray-950 sm:py-28">
  <div class="mx-auto max-w-7xl px-6">
    <div class="mx-auto max-w-2xl text-center">
      <h2
        class="text-3xl font-bold tracking-tight text-gray-900 dark:text-white sm:text-4xl"
      >
        Everything you need to ship
      </h2>
      <p class="mt-4 text-lg text-gray-600 dark:text-gray-400">
        Built for modern teams that move fast and demand reliability.
      </p>
    </div>

    <div class="mt-16 grid gap-8 sm:grid-cols-2 lg:grid-cols-3">
      <!-- Feature Card -->
      <div class="rounded-2xl border border-gray-200 p-8 dark:border-gray-800">
        <div
          class="flex h-12 w-12 items-center justify-center rounded-xl bg-indigo-50 text-indigo-600 dark:bg-indigo-950 dark:text-indigo-400"
        >
          <!-- Icon SVG here -->
          <svg
            class="h-6 w-6"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M3.75 13.5l10.5-11.25L12 10.5h8.25L9.75 21.75 12 13.5H3.75z"
            />
          </svg>
        </div>
        <h3 class="mt-6 text-lg font-semibold text-gray-900 dark:text-white">
          Lightning deploys
        </h3>
        <p
          class="mt-2 text-sm leading-relaxed text-gray-600 dark:text-gray-400"
        >
          Push to production in under 30 seconds with zero-downtime deployments
          and automatic rollbacks.
        </p>
      </div>

      <!-- Repeat for additional features -->
    </div>
  </div>
</section>
```

## Testimonial Section

```html
<section class="bg-gray-50 py-20 dark:bg-gray-900 sm:py-28">
  <div class="mx-auto max-w-7xl px-6">
    <h2
      class="text-center text-3xl font-bold tracking-tight text-gray-900 dark:text-white sm:text-4xl"
    >
      Loved by developers
    </h2>

    <div class="mt-16 grid gap-8 sm:grid-cols-2 lg:grid-cols-3">
      <!-- Testimonial Card -->
      <figure
        class="rounded-2xl bg-white p-8 shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700"
      >
        <blockquote
          class="text-sm leading-relaxed text-gray-700 dark:text-gray-300"
        >
          &ldquo;We cut our deployment time by 80%. The team ships with
          confidence now and our release cadence tripled.&rdquo;
        </blockquote>
        <figcaption class="mt-6 flex items-center gap-4">
          <img
            src="/avatars/jane.jpg"
            alt=""
            class="h-10 w-10 rounded-full object-cover"
          />
          <div>
            <p class="text-sm font-semibold text-gray-900 dark:text-white">
              Jane Smith
            </p>
            <p class="text-xs text-gray-500 dark:text-gray-400">
              CTO, Acme Corp
            </p>
          </div>
        </figcaption>
      </figure>

      <!-- Repeat for more testimonials -->
    </div>
  </div>
</section>
```

## Pricing CTA Block

```html
<section class="bg-indigo-600 py-20 sm:py-28">
  <div class="mx-auto max-w-4xl px-6 text-center">
    <h2 class="text-3xl font-bold tracking-tight text-white sm:text-4xl">
      Ready to ship faster?
    </h2>
    <p class="mx-auto mt-4 max-w-xl text-lg text-indigo-100">
      Start your free 14-day trial. No credit card required.
    </p>
    <div
      class="mt-10 flex flex-col items-center gap-4 sm:flex-row sm:justify-center"
    >
      <a
        href="#"
        class="rounded-full bg-white px-8 py-3.5 text-sm font-semibold text-indigo-600 shadow-lg hover:bg-indigo-50 transition-colors"
      >
        Start free trial
      </a>
      <a
        href="#"
        class="text-sm font-semibold text-white hover:text-indigo-100 transition-colors"
      >
        Talk to sales <span aria-hidden="true">&rarr;</span>
      </a>
    </div>
  </div>
</section>
```

## FAQ Accordion

```html
<section class="bg-white py-20 dark:bg-gray-950 sm:py-28">
  <div class="mx-auto max-w-3xl px-6">
    <h2
      class="text-center text-3xl font-bold tracking-tight text-gray-900 dark:text-white sm:text-4xl"
    >
      Frequently asked questions
    </h2>

    <dl class="mt-12 divide-y divide-gray-200 dark:divide-gray-800">
      <!-- FAQ Item — uses <details> for no-JS accordion -->
      <details class="group py-6">
        <summary
          class="flex cursor-pointer items-center justify-between text-left text-base font-semibold text-gray-900 dark:text-white"
        >
          What happens when my trial ends?
          <span
            class="ml-4 flex-shrink-0 transition-transform group-open:rotate-45"
          >
            <svg
              class="h-5 w-5"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="2"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M12 4.5v15m7.5-7.5h-15"
              />
            </svg>
          </span>
        </summary>
        <dd
          class="mt-4 text-sm leading-relaxed text-gray-600 dark:text-gray-400"
        >
          Your projects remain accessible in read-only mode. Upgrade anytime to
          restore full functionality — no data is ever deleted.
        </dd>
      </details>

      <!-- Repeat for more FAQ items -->
    </dl>
  </div>
</section>
```

## Footer with Secondary CTAs

```html
<footer
  class="border-t border-gray-200 bg-gray-50 dark:border-gray-800 dark:bg-gray-900"
>
  <div class="mx-auto max-w-7xl px-6 py-12 lg:py-16">
    <div class="grid gap-8 sm:grid-cols-2 lg:grid-cols-4">
      <!-- Column 1 — Brand -->
      <div>
        <p class="text-lg font-bold text-gray-900 dark:text-white">
          ProductName
        </p>
        <p class="mt-2 text-sm text-gray-600 dark:text-gray-400">
          Ship faster, sleep better.
        </p>
      </div>
      <!-- Column 2 — Product -->
      <div>
        <h3 class="text-sm font-semibold text-gray-900 dark:text-white">
          Product
        </h3>
        <ul class="mt-4 space-y-2 text-sm text-gray-600 dark:text-gray-400">
          <li>
            <a
              href="#"
              class="hover:text-gray-900 dark:hover:text-white transition-colors"
              >Features</a
            >
          </li>
          <li>
            <a
              href="#"
              class="hover:text-gray-900 dark:hover:text-white transition-colors"
              >Pricing</a
            >
          </li>
          <li>
            <a
              href="#"
              class="hover:text-gray-900 dark:hover:text-white transition-colors"
              >Changelog</a
            >
          </li>
        </ul>
      </div>
      <!-- Column 3 — Company -->
      <div>
        <h3 class="text-sm font-semibold text-gray-900 dark:text-white">
          Company
        </h3>
        <ul class="mt-4 space-y-2 text-sm text-gray-600 dark:text-gray-400">
          <li>
            <a
              href="#"
              class="hover:text-gray-900 dark:hover:text-white transition-colors"
              >About</a
            >
          </li>
          <li>
            <a
              href="#"
              class="hover:text-gray-900 dark:hover:text-white transition-colors"
              >Blog</a
            >
          </li>
          <li>
            <a
              href="#"
              class="hover:text-gray-900 dark:hover:text-white transition-colors"
              >Careers</a
            >
          </li>
        </ul>
      </div>
      <!-- Column 4 — Legal -->
      <div>
        <h3 class="text-sm font-semibold text-gray-900 dark:text-white">
          Legal
        </h3>
        <ul class="mt-4 space-y-2 text-sm text-gray-600 dark:text-gray-400">
          <li>
            <a
              href="#"
              class="hover:text-gray-900 dark:hover:text-white transition-colors"
              >Privacy</a
            >
          </li>
          <li>
            <a
              href="#"
              class="hover:text-gray-900 dark:hover:text-white transition-colors"
              >Terms</a
            >
          </li>
        </ul>
      </div>
    </div>

    <div class="mt-12 border-t border-gray-200 pt-8 dark:border-gray-800">
      <p class="text-center text-xs text-gray-500 dark:text-gray-400">
        &copy; 2026 ProductName. All rights reserved.
      </p>
    </div>
  </div>
</footer>
```

## Copywriting Guidelines

### Headline Formulas

| Formula                  | Example                                       |
| ------------------------ | --------------------------------------------- |
| **Outcome without pain** | "Ship faster without breaking things"         |
| **Number + benefit**     | "3x faster deployments, zero downtime"        |
| **Question**             | "Still deploying manually?"                   |
| **How to**               | "How 2,000+ teams deploy with confidence"     |
| **Contrast**             | "From 30-minute deploys to 30-second deploys" |

### Subheadline Patterns

- Expand on the headline with specifics (who, what, how).
- Keep to 1–2 sentences, under 30 words.
- Address a secondary benefit or reduce a perceived risk.

### CTA Text

| Weak       | Strong                    |
| ---------- | ------------------------- |
| Submit     | Start free trial          |
| Click here | Get started — it's free   |
| Learn more | See how it works          |
| Sign up    | Create your first project |

- Use first person when appropriate: "Start **my** free trial."
- Add urgency or risk-reduction: "No credit card required."

## Responsive Design

- **Mobile-first**: design for small screens, then enhance for larger ones.
- **Stack on mobile**: multi-column grids collapse to single column below `sm:`.
- **Touch targets**: minimum 44×44px for all tappable elements.
- **Font scaling**: use `text-base` on mobile, scale up with `sm:text-lg`, `lg:text-xl`.
- **Image handling**: use `srcset` and `sizes` attributes; serve WebP/AVIF.
- **Hero height**: avoid `100vh` on mobile (address bar issues). Use `min-h-[calc(100dvh-4rem)]` or similar dynamic viewport units.

## Performance Optimization

- **Above the fold**: load hero image eagerly (`loading="eager"`), defer everything else.
- **Fonts**: self-host, use `font-display: swap`, subset to required characters.
- **Images**: lazy-load below fold, use `width`/`height` attributes to prevent CLS.
- **CSS**: Tailwind purges unused classes automatically — keep the build pipeline configured.
- **JavaScript**: defer non-critical scripts; inline critical JS if under 1 KB.
- **Core Web Vitals targets**: LCP < 2.5s, INP < 200ms, CLS < 0.1.

## Accessibility Checklist

- All images have descriptive `alt` text (or `alt=""` for decorative images).
- Color contrast meets WCAG AA (4.5:1 for text, 3:1 for large text).
- Focus styles are visible on all interactive elements.
- Semantic HTML: `<header>`, `<main>`, `<section>`, `<footer>`, `<nav>`.
- Skip-to-content link as the first focusable element.
- FAQ accordions are keyboard-navigable (native `<details>` handles this).
- CTA buttons use `<a>` for navigation or `<button>` for actions — never `<div>`.

## Anti-Patterns

- **Wall of text** above the fold — the hero should be scannable in 3 seconds.
- **Multiple competing CTAs** — one primary action per viewport.
- **Stock photography** that feels generic — use product screenshots or custom illustrations.
- **Auto-playing video with sound** — always mute by default with a play control.
- **Hiding pricing** — if users want pricing, make it easy to find or they will leave.
- **No mobile testing** — always verify on real devices; responsive mode in DevTools is approximate.
- **Ignoring page speed** — a 1-second delay in LCP can reduce conversions by 7%.
