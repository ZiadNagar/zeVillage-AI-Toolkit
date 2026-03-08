---
name: features-page
description: "Use this skill when designing and building product features pages — bento grids, alternating image-text rows, icon feature grids, scroll-triggered animations, integration logo showcases, metric sections, and responsive layouts with Tailwind CSS."
license: Complete terms in LICENSE.txt
---

# Features / Product Page Design

## When to Use

- Building a product features page that showcases capabilities.
- Designing bento grid layouts, alternating image-text sections, or icon feature grids.
- Adding scroll-triggered reveal animations to feature sections.
- Creating integration/partner logo displays or metric showcase sections.
- Structuring a features page with hero, deep-dives, comparisons, and CTAs.

## Page Structure

A features page guides users from high-level value to specific proof:

1. **Hero** — product headline with screenshot or demo visual.
2. **Feature Overview** — icon grid or bento grid showing key capabilities at a glance.
3. **Feature Deep-Dives** — alternating image-text rows with detail per feature.
4. **Integration Showcase** — logo grid of supported platforms and tools.
5. **Metrics / Social Proof** — stats, testimonials, or case-study snippets.
6. **Competitor Comparison** (optional) — feature checklist vs. alternatives.
7. **CTA Section** — clear next step (trial, demo, sign-up).

## Hero with Product Screenshot

```html
<section class="bg-white dark:bg-gray-950">
  <div class="mx-auto max-w-7xl px-6 py-24 sm:py-32">
    <div class="mx-auto max-w-3xl text-center">
      <p
        class="text-sm font-semibold tracking-wider text-indigo-600 uppercase dark:text-indigo-400"
      >
        Platform
      </p>
      <h1
        class="mt-4 text-4xl font-bold tracking-tight text-gray-900 dark:text-white sm:text-6xl"
      >
        Everything you need to build, ship, and scale
      </h1>
      <p class="mt-6 text-lg leading-relaxed text-gray-600 dark:text-gray-400">
        A complete toolkit for modern development teams. From local dev to
        global production in minutes.
      </p>
    </div>

    <!-- Product Screenshot -->
    <div class="relative mx-auto mt-16 max-w-5xl">
      <div
        class="rounded-2xl bg-gray-900/5 p-2 ring-1 ring-gray-900/10 dark:bg-white/5 dark:ring-white/10"
      >
        <img
          src="/product-screenshot.png"
          alt="Product dashboard showing deployment pipeline"
          class="rounded-xl shadow-2xl"
          width="1200"
          height="750"
          loading="eager"
        />
      </div>
    </div>
  </div>
</section>
```

## Bento Grid Layout

A bento grid uses varied card sizes to create visual hierarchy — larger cards for primary features, smaller for secondary.

```html
<section class="bg-gray-50 py-20 dark:bg-gray-900 sm:py-28">
  <div class="mx-auto max-w-7xl px-6">
    <div class="mx-auto max-w-2xl text-center">
      <h2
        class="text-3xl font-bold tracking-tight text-gray-900 dark:text-white sm:text-4xl"
      >
        Built for every workflow
      </h2>
    </div>

    <div class="mt-16 grid gap-4 sm:grid-cols-2 lg:grid-cols-3">
      <!-- Large card — spans 2 columns -->
      <div
        class="relative overflow-hidden rounded-2xl bg-white p-8 shadow-sm ring-1 ring-gray-200 sm:col-span-2 dark:bg-gray-800 dark:ring-gray-700"
      >
        <h3 class="text-lg font-semibold text-gray-900 dark:text-white">
          Real-time collaboration
        </h3>
        <p
          class="mt-2 max-w-lg text-sm leading-relaxed text-gray-600 dark:text-gray-400"
        >
          Work together with your team in real time. See cursors, edits, and
          comments live — no refresh needed.
        </p>
        <img
          src="/features/collaboration.png"
          alt="Collaboration interface"
          class="mt-8 rounded-lg ring-1 ring-gray-200 dark:ring-gray-700"
          width="600"
          height="350"
          loading="lazy"
        />
      </div>

      <!-- Standard card -->
      <div
        class="rounded-2xl bg-white p-8 shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700"
      >
        <div
          class="flex h-10 w-10 items-center justify-center rounded-lg bg-indigo-50 text-indigo-600 dark:bg-indigo-950 dark:text-indigo-400"
        >
          <svg
            class="h-5 w-5"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M16.5 10.5V6.75a4.5 4.5 0 10-9 0v3.75m-.75 11.25h10.5a2.25 2.25 0 002.25-2.25v-6.75a2.25 2.25 0 00-2.25-2.25H6.75a2.25 2.25 0 00-2.25 2.25v6.75a2.25 2.25 0 002.25 2.25z"
            />
          </svg>
        </div>
        <h3 class="mt-4 text-lg font-semibold text-gray-900 dark:text-white">
          Enterprise security
        </h3>
        <p
          class="mt-2 text-sm leading-relaxed text-gray-600 dark:text-gray-400"
        >
          SOC 2 Type II compliant. SSO, RBAC, audit logs, and encryption at rest
          and in transit.
        </p>
      </div>

      <!-- Standard card -->
      <div
        class="rounded-2xl bg-white p-8 shadow-sm ring-1 ring-gray-200 dark:bg-gray-800 dark:ring-gray-700"
      >
        <div
          class="flex h-10 w-10 items-center justify-center rounded-lg bg-indigo-50 text-indigo-600 dark:bg-indigo-950 dark:text-indigo-400"
        >
          <svg
            class="h-5 w-5"
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
        <h3 class="mt-4 text-lg font-semibold text-gray-900 dark:text-white">
          Edge functions
        </h3>
        <p
          class="mt-2 text-sm leading-relaxed text-gray-600 dark:text-gray-400"
        >
          Deploy serverless functions to 30+ global regions. Sub-50ms cold
          starts.
        </p>
      </div>

      <!-- Large card — spans 2 columns -->
      <div
        class="relative overflow-hidden rounded-2xl bg-white p-8 shadow-sm ring-1 ring-gray-200 sm:col-span-2 dark:bg-gray-800 dark:ring-gray-700"
      >
        <h3 class="text-lg font-semibold text-gray-900 dark:text-white">
          Advanced analytics
        </h3>
        <p
          class="mt-2 max-w-lg text-sm leading-relaxed text-gray-600 dark:text-gray-400"
        >
          Track performance, errors, and user behavior with built-in
          observability tools. No third-party scripts needed.
        </p>
        <img
          src="/features/analytics.png"
          alt="Analytics dashboard"
          class="mt-8 rounded-lg ring-1 ring-gray-200 dark:ring-gray-700"
          width="600"
          height="350"
          loading="lazy"
        />
      </div>
    </div>
  </div>
</section>
```

## Alternating Feature Rows

```html
<section class="bg-white py-20 dark:bg-gray-950 sm:py-28">
  <div class="mx-auto max-w-7xl px-6 space-y-24 lg:space-y-32">
    <!-- Row 1: Image Right -->
    <div class="grid items-center gap-12 lg:grid-cols-2">
      <div>
        <p class="text-sm font-semibold text-indigo-600 dark:text-indigo-400">
          Deploy
        </p>
        <h3
          class="mt-2 text-2xl font-bold text-gray-900 dark:text-white sm:text-3xl"
        >
          Push to production in seconds
        </h3>
        <p
          class="mt-4 text-base leading-relaxed text-gray-600 dark:text-gray-400"
        >
          Every git push triggers an automatic build and deploy. Preview
          deployments for every pull request. Roll back to any previous version
          with one click.
        </p>
        <ul class="mt-6 space-y-3 text-sm text-gray-600 dark:text-gray-400">
          <li class="flex items-center gap-3">
            <svg
              class="h-5 w-5 shrink-0 text-indigo-500"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="2"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M4.5 12.75l6 6 9-13.5"
              />
            </svg>
            Zero-downtime deployments
          </li>
          <li class="flex items-center gap-3">
            <svg
              class="h-5 w-5 shrink-0 text-indigo-500"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="2"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M4.5 12.75l6 6 9-13.5"
              />
            </svg>
            Automatic preview URLs for every PR
          </li>
          <li class="flex items-center gap-3">
            <svg
              class="h-5 w-5 shrink-0 text-indigo-500"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="2"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M4.5 12.75l6 6 9-13.5"
              />
            </svg>
            Instant rollback to any version
          </li>
        </ul>
      </div>
      <div>
        <img
          src="/features/deploy.png"
          alt="Deployment pipeline showing build stages"
          class="rounded-2xl shadow-xl ring-1 ring-gray-200 dark:ring-gray-800"
          width="600"
          height="400"
          loading="lazy"
        />
      </div>
    </div>

    <!-- Row 2: Image Left (reversed) -->
    <div class="grid items-center gap-12 lg:grid-cols-2">
      <div class="order-last lg:order-first">
        <img
          src="/features/monitor.png"
          alt="Monitoring dashboard with real-time metrics"
          class="rounded-2xl shadow-xl ring-1 ring-gray-200 dark:ring-gray-800"
          width="600"
          height="400"
          loading="lazy"
        />
      </div>
      <div>
        <p class="text-sm font-semibold text-indigo-600 dark:text-indigo-400">
          Monitor
        </p>
        <h3
          class="mt-2 text-2xl font-bold text-gray-900 dark:text-white sm:text-3xl"
        >
          Know before your users do
        </h3>
        <p
          class="mt-4 text-base leading-relaxed text-gray-600 dark:text-gray-400"
        >
          Real-time error tracking, performance monitoring, and usage analytics.
          Get alerted the moment something goes wrong.
        </p>
        <ul class="mt-6 space-y-3 text-sm text-gray-600 dark:text-gray-400">
          <li class="flex items-center gap-3">
            <svg
              class="h-5 w-5 shrink-0 text-indigo-500"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="2"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M4.5 12.75l6 6 9-13.5"
              />
            </svg>
            Real-time error tracking
          </li>
          <li class="flex items-center gap-3">
            <svg
              class="h-5 w-5 shrink-0 text-indigo-500"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="2"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M4.5 12.75l6 6 9-13.5"
              />
            </svg>
            Web Vitals monitoring
          </li>
          <li class="flex items-center gap-3">
            <svg
              class="h-5 w-5 shrink-0 text-indigo-500"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="2"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M4.5 12.75l6 6 9-13.5"
              />
            </svg>
            Custom alert rules via webhook or email
          </li>
        </ul>
      </div>
    </div>
  </div>
</section>
```

## Icon Feature Grid

```html
<section class="bg-white py-20 dark:bg-gray-950 sm:py-28">
  <div class="mx-auto max-w-7xl px-6">
    <div class="mx-auto max-w-2xl text-center">
      <h2
        class="text-3xl font-bold tracking-tight text-gray-900 dark:text-white sm:text-4xl"
      >
        And so much more
      </h2>
      <p class="mt-4 text-lg text-gray-600 dark:text-gray-400">
        Every feature you need to build at scale.
      </p>
    </div>

    <div class="mt-16 grid gap-x-8 gap-y-12 sm:grid-cols-2 lg:grid-cols-4">
      <!-- Feature item -->
      <div>
        <div
          class="flex h-10 w-10 items-center justify-center rounded-lg bg-indigo-50 text-indigo-600 dark:bg-indigo-950 dark:text-indigo-400"
        >
          <svg
            class="h-5 w-5"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M12 21a9.004 9.004 0 008.716-6.747M12 21a9.004 9.004 0 01-8.716-6.747M12 21c2.485 0 4.5-4.03 4.5-9S14.485 3 12 3m0 18c-2.485 0-4.5-4.03-4.5-9S9.515 3 12 3m0 0a8.997 8.997 0 017.843 4.582M12 3a8.997 8.997 0 00-7.843 4.582"
            />
          </svg>
        </div>
        <h3 class="mt-4 text-sm font-semibold text-gray-900 dark:text-white">
          Global CDN
        </h3>
        <p class="mt-1 text-sm text-gray-500 dark:text-gray-400">
          Content served from 70+ edge locations worldwide.
        </p>
      </div>

      <!-- Feature item -->
      <div>
        <div
          class="flex h-10 w-10 items-center justify-center rounded-lg bg-indigo-50 text-indigo-600 dark:bg-indigo-950 dark:text-indigo-400"
        >
          <svg
            class="h-5 w-5"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M9 12.75L11.25 15 15 9.75m-3-7.036A11.959 11.959 0 013.598 6 11.99 11.99 0 003 9.749c0 5.592 3.824 10.29 9 11.623 5.176-1.332 9-6.03 9-11.622 0-1.31-.21-2.571-.598-3.751h-.152c-3.196 0-6.1-1.248-8.25-3.285z"
            />
          </svg>
        </div>
        <h3 class="mt-4 text-sm font-semibold text-gray-900 dark:text-white">
          DDoS protection
        </h3>
        <p class="mt-1 text-sm text-gray-500 dark:text-gray-400">
          Automatic mitigation at the edge. No config needed.
        </p>
      </div>

      <!-- Feature item -->
      <div>
        <div
          class="flex h-10 w-10 items-center justify-center rounded-lg bg-indigo-50 text-indigo-600 dark:bg-indigo-950 dark:text-indigo-400"
        >
          <svg
            class="h-5 w-5"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M14.25 9.75L16.5 12l-2.25 2.25m-4.5 0L7.5 12l2.25-2.25M6 20.25h12A2.25 2.25 0 0020.25 18V6A2.25 2.25 0 0018 3.75H6A2.25 2.25 0 003.75 6v12A2.25 2.25 0 006 20.25z"
            />
          </svg>
        </div>
        <h3 class="mt-4 text-sm font-semibold text-gray-900 dark:text-white">
          API routes
        </h3>
        <p class="mt-1 text-sm text-gray-500 dark:text-gray-400">
          Build backend endpoints alongside your frontend code.
        </p>
      </div>

      <!-- Feature item -->
      <div>
        <div
          class="flex h-10 w-10 items-center justify-center rounded-lg bg-indigo-50 text-indigo-600 dark:bg-indigo-950 dark:text-indigo-400"
        >
          <svg
            class="h-5 w-5"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M3.75 3v11.25A2.25 2.25 0 006 16.5h2.25M3.75 3h-1.5m1.5 0h16.5m0 0h1.5m-1.5 0v11.25A2.25 2.25 0 0118 16.5h-2.25m-7.5 0h7.5m-7.5 0l-1 3m8.5-3l1 3m0 0l.5 1.5m-.5-1.5h-9.5m0 0l-.5 1.5"
            />
          </svg>
        </div>
        <h3 class="mt-4 text-sm font-semibold text-gray-900 dark:text-white">
          Preview deployments
        </h3>
        <p class="mt-1 text-sm text-gray-500 dark:text-gray-400">
          Every branch gets its own URL for review and testing.
        </p>
      </div>

      <!-- Add more feature items as needed -->
    </div>
  </div>
</section>
```

## Animated Scroll Reveals

Use the Intersection Observer API to trigger entrance animations when sections scroll into view.

```js
function initScrollReveal() {
  const observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          entry.target.classList.add("revealed");
          observer.unobserve(entry.target);
        }
      });
    },
    { threshold: 0.15, rootMargin: "0px 0px -60px 0px" },
  );

  document
    .querySelectorAll("[data-reveal]")
    .forEach((el) => observer.observe(el));
}

document.addEventListener("DOMContentLoaded", initScrollReveal);
```

```css
/* Base hidden state */
[data-reveal] {
  opacity: 0;
  transform: translateY(24px);
  transition:
    opacity 0.6s ease-out,
    transform 0.6s ease-out;
}

/* Revealed state */
[data-reveal].revealed {
  opacity: 1;
  transform: translateY(0);
}

/* Stagger children */
[data-reveal-stagger] > * {
  opacity: 0;
  transform: translateY(16px);
  transition:
    opacity 0.5s ease-out,
    transform 0.5s ease-out;
}

[data-reveal-stagger].revealed > *:nth-child(1) {
  transition-delay: 0ms;
}
[data-reveal-stagger].revealed > *:nth-child(2) {
  transition-delay: 80ms;
}
[data-reveal-stagger].revealed > *:nth-child(3) {
  transition-delay: 160ms;
}
[data-reveal-stagger].revealed > *:nth-child(4) {
  transition-delay: 240ms;
}

[data-reveal-stagger].revealed > * {
  opacity: 1;
  transform: translateY(0);
}

/* Respect reduced motion */
@media (prefers-reduced-motion: reduce) {
  [data-reveal],
  [data-reveal-stagger] > * {
    opacity: 1;
    transform: none;
    transition: none;
  }
}
```

Apply to any section:

```html
<div data-reveal>
  <!-- This element fades up when scrolled into view -->
</div>

<div class="grid gap-8 sm:grid-cols-2 lg:grid-cols-4" data-reveal-stagger>
  <div>Feature 1</div>
  <div>Feature 2</div>
  <div>Feature 3</div>
  <div>Feature 4</div>
</div>
```

## Integration / Partner Logo Grid

```html
<section
  class="border-y border-gray-200 bg-gray-50 py-16 dark:border-gray-800 dark:bg-gray-900"
>
  <div class="mx-auto max-w-7xl px-6">
    <h2 class="text-center text-lg font-semibold text-gray-900 dark:text-white">
      Integrates with the tools you already use
    </h2>

    <div
      class="mt-10 grid grid-cols-3 items-center gap-8 sm:grid-cols-4 lg:grid-cols-6"
    >
      <div class="flex items-center justify-center">
        <img
          src="/logos/github.svg"
          alt="GitHub"
          class="h-8 opacity-60 grayscale hover:opacity-100 hover:grayscale-0 transition"
        />
      </div>
      <div class="flex items-center justify-center">
        <img
          src="/logos/slack.svg"
          alt="Slack"
          class="h-8 opacity-60 grayscale hover:opacity-100 hover:grayscale-0 transition"
        />
      </div>
      <div class="flex items-center justify-center">
        <img
          src="/logos/jira.svg"
          alt="Jira"
          class="h-8 opacity-60 grayscale hover:opacity-100 hover:grayscale-0 transition"
        />
      </div>
      <div class="flex items-center justify-center">
        <img
          src="/logos/figma.svg"
          alt="Figma"
          class="h-8 opacity-60 grayscale hover:opacity-100 hover:grayscale-0 transition"
        />
      </div>
      <div class="flex items-center justify-center">
        <img
          src="/logos/datadog.svg"
          alt="Datadog"
          class="h-8 opacity-60 grayscale hover:opacity-100 hover:grayscale-0 transition"
        />
      </div>
      <div class="flex items-center justify-center">
        <img
          src="/logos/pagerduty.svg"
          alt="PagerDuty"
          class="h-8 opacity-60 grayscale hover:opacity-100 hover:grayscale-0 transition"
        />
      </div>
    </div>
  </div>
</section>
```

## Metric / Stat Showcase

```html
<section class="bg-white py-16 dark:bg-gray-950">
  <div class="mx-auto max-w-7xl px-6">
    <div class="grid gap-8 text-center sm:grid-cols-2 lg:grid-cols-4">
      <div>
        <p class="text-4xl font-bold text-indigo-600 dark:text-indigo-400">
          99.99%
        </p>
        <p class="mt-2 text-sm font-medium text-gray-600 dark:text-gray-400">
          Uptime SLA
        </p>
      </div>
      <div>
        <p class="text-4xl font-bold text-indigo-600 dark:text-indigo-400">
          50ms
        </p>
        <p class="mt-2 text-sm font-medium text-gray-600 dark:text-gray-400">
          Avg. response time
        </p>
      </div>
      <div>
        <p class="text-4xl font-bold text-indigo-600 dark:text-indigo-400">
          10M+
        </p>
        <p class="mt-2 text-sm font-medium text-gray-600 dark:text-gray-400">
          Deployments served
        </p>
      </div>
      <div>
        <p class="text-4xl font-bold text-indigo-600 dark:text-indigo-400">
          70+
        </p>
        <p class="mt-2 text-sm font-medium text-gray-600 dark:text-gray-400">
          Edge regions
        </p>
      </div>
    </div>
  </div>
</section>
```

## Responsive Design Patterns

- **Bento grid**: use `sm:col-span-2` for featured cards. Falls to single column on mobile.
- **Alternating rows**: stack image above text on mobile with `order-last lg:order-first`.
- **Icon grid**: `grid-cols-1 sm:grid-cols-2 lg:grid-cols-4` scales from 1 to 4 columns.
- **Logo grid**: `grid-cols-3 sm:grid-cols-4 lg:grid-cols-6` ensures logos never feel cramped.
- **Stats**: `grid-cols-2 lg:grid-cols-4` keeps metrics readable on all screens.
- **Images**: always include `width`, `height`, and `loading="lazy"` (except hero).

## Best Practices

- **Lead with value, not features**: frame each feature in terms of what it enables for the user.
- **Show, don't tell**: pair every text section with a screenshot, illustration, or demo GIF.
- **Limit feature count**: highlight 4–8 key features rather than listing everything.
- **Progressive disclosure**: overview grid first, then deep-dives for users who scroll.
- **Consistent visual language**: use the same icon style, card design, and color scheme throughout.
- **Performance**: lazy-load images below the fold; optimize screenshots with WebP/AVIF.

## Anti-Patterns

- **Feature dumping** — listing 20+ features without hierarchy overwhelms visitors.
- **No visuals** — text-only feature descriptions feel like documentation, not marketing.
- **Inconsistent layout** — mixing too many layout patterns creates visual chaos.
- **Missing CTAs** — every section group should end with a clear next step.
- **Ignoring mobile** — always test alternating layouts, grids, and images on narrow viewports.
- **Animations without reduced-motion** — always implement `prefers-reduced-motion: reduce`.
