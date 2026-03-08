---
name: pricing-page-design
description: "Use this skill when designing and building pricing pages — tier card layouts, plan comparison tables, billing toggle switches, feature checklists, recommended plan highlighting, enterprise CTAs, and conversion-optimized responsive designs with Tailwind CSS."
license: Complete terms in LICENSE.txt
---

# Pricing Page Design

## When to Use

- Building a SaaS pricing page with multiple plan tiers.
- Designing a billing period toggle (monthly/annual).
- Creating plan comparison tables with feature checklists.
- Highlighting a recommended or most-popular plan.
- Adding enterprise/custom plan CTAs alongside self-serve plans.

## Page Structure

A well-designed pricing page typically includes:

1. **Header** — clear headline, optional subheadline explaining the pricing philosophy.
2. **Billing Toggle** — monthly vs. annual switch with savings badge.
3. **Pricing Cards** — 2–4 tier cards side by side (3 is most common).
4. **Comparison Table** — full feature breakdown across tiers.
5. **FAQ Section** — billing, refund, and plan-change questions.
6. **Trust Signals** — money-back guarantee, security badges, testimonials.
7. **Enterprise CTA** — custom plan callout for larger organizations.

## Billing Period Toggle

```html
<div class="flex items-center justify-center gap-3">
  <span id="monthly-label" class="text-sm font-medium text-gray-900 dark:text-white">Monthly</span>

  <button
    type="button"
    role="switch"
    aria-checked="false"
    aria-labelledby="monthly-label annual-label"
    class="group relative inline-flex h-6 w-11 shrink-0 cursor-pointer rounded-full border-2 border-transparent bg-gray-200 transition-colors focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600 dark:bg-gray-700"
    data-billing-toggle
  >
    <span class="pointer-events-none relative inline-block h-5 w-5 rounded-full bg-white shadow ring-0 transition-transform translate-x-0 group-aria-checked:translate-x-5">
    </span>
  </button>

  <span id="annual-label" class="text-sm font-medium text-gray-500 dark:text-gray-400">
    Annual
    <span class="ml-1 inline-flex items-center rounded-full bg-green-50 px-2 py-0.5 text-xs font-medium text-green-700 ring-1 ring-green-600/20 ring-inset dark:bg-green-900/30 dark:text-green-400 dark:ring-green-500/30">
      Save 20%
    </span>
  </span>
</div>
```

### Toggle JavaScript

```js
const toggle = document.querySelector("[data-billing-toggle]");
const monthlyPrices = document.querySelectorAll("[data-monthly]");
const annualPrices = document.querySelectorAll("[data-annual]");

toggle.addEventListener("click", () => {
  const isAnnual = toggle.getAttribute("aria-checked") === "true";
  toggle.setAttribute("aria-checked", String(!isAnnual));

  monthlyPrices.forEach((el) => el.classList.toggle("hidden", !isAnnual));
  annualPrices.forEach((el) => el.classList.toggle("hidden", isAnnual));
});
```

## Pricing Card Grid

### Three-Column Layout

```html
<section class="bg-white py-20 dark:bg-gray-950 sm:py-28">
  <div class="mx-auto max-w-7xl px-6">
    <!-- Section Header -->
    <div class="mx-auto max-w-2xl text-center">
      <h2 class="text-3xl font-bold tracking-tight text-gray-900 dark:text-white sm:text-4xl">
        Simple, transparent pricing
      </h2>
      <p class="mt-4 text-lg text-gray-600 dark:text-gray-400">
        No hidden fees. Upgrade, downgrade, or cancel anytime.
      </p>
    </div>

    <!-- Billing Toggle (insert toggle component here) -->
    <div class="mt-10 flex justify-center">
      <!-- toggle component -->
    </div>

    <!-- Pricing Cards -->
    <div class="mt-12 grid gap-8 lg:grid-cols-3">

      <!-- Starter Plan -->
      <div class="rounded-2xl border border-gray-200 p-8 dark:border-gray-800">
        <h3 class="text-lg font-semibold text-gray-900 dark:text-white">Starter</h3>
        <p class="mt-2 text-sm text-gray-500 dark:text-gray-400">For individuals and side projects.</p>

        <p class="mt-6">
          <span class="text-4xl font-bold text-gray-900 dark:text-white" data-monthly>$9</span>
          <span class="text-4xl font-bold text-gray-900 dark:text-white hidden" data-annual>$7</span>
          <span class="text-sm text-gray-500 dark:text-gray-400">/month</span>
        </p>

        <a href="#" class="mt-8 block rounded-lg border border-gray-300 py-2.5 text-center text-sm font-semibold text-gray-700 hover:bg-gray-50 transition-colors dark:border-gray-700 dark:text-gray-300 dark:hover:bg-gray-900">
          Get started
        </a>

        <ul class="mt-8 space-y-3 text-sm text-gray-600 dark:text-gray-400">
          <li class="flex items-start gap-3">
            <svg class="mt-0.5 h-5 w-5 shrink-0 text-indigo-500" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" /></svg>
            3 projects
          </li>
          <li class="flex items-start gap-3">
            <svg class="mt-0.5 h-5 w-5 shrink-0 text-indigo-500" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" /></svg>
            1 GB storage
          </li>
          <li class="flex items-start gap-3">
            <svg class="mt-0.5 h-5 w-5 shrink-0 text-indigo-500" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" /></svg>
            Community support
          </li>
        </ul>
      </div>

      <!-- Pro Plan (Recommended) -->
      <div class="relative rounded-2xl border-2 border-indigo-600 p-8 shadow-lg dark:border-indigo-500">
        <span class="absolute -top-3.5 left-1/2 -translate-x-1/2 rounded-full bg-indigo-600 px-4 py-1 text-xs font-semibold text-white">
          Most popular
        </span>

        <h3 class="text-lg font-semibold text-gray-900 dark:text-white">Pro</h3>
        <p class="mt-2 text-sm text-gray-500 dark:text-gray-400">For growing teams that need more power.</p>

        <p class="mt-6">
          <span class="text-4xl font-bold text-gray-900 dark:text-white" data-monthly>$29</span>
          <span class="text-4xl font-bold text-gray-900 dark:text-white hidden" data-annual>$23</span>
          <span class="text-sm text-gray-500 dark:text-gray-400">/month</span>
        </p>

        <a href="#" class="mt-8 block rounded-lg bg-indigo-600 py-2.5 text-center text-sm font-semibold text-white shadow-md hover:bg-indigo-500 transition-colors">
          Start free trial
        </a>

        <ul class="mt-8 space-y-3 text-sm text-gray-600 dark:text-gray-400">
          <li class="flex items-start gap-3">
            <svg class="mt-0.5 h-5 w-5 shrink-0 text-indigo-500" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" /></svg>
            Unlimited projects
          </li>
          <li class="flex items-start gap-3">
            <svg class="mt-0.5 h-5 w-5 shrink-0 text-indigo-500" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" /></svg>
            50 GB storage
          </li>
          <li class="flex items-start gap-3">
            <svg class="mt-0.5 h-5 w-5 shrink-0 text-indigo-500" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" /></svg>
            Priority support
          </li>
          <li class="flex items-start gap-3">
            <svg class="mt-0.5 h-5 w-5 shrink-0 text-indigo-500" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" /></svg>
            Advanced analytics
          </li>
        </ul>
      </div>

      <!-- Enterprise Plan -->
      <div class="rounded-2xl border border-gray-200 p-8 dark:border-gray-800">
        <h3 class="text-lg font-semibold text-gray-900 dark:text-white">Enterprise</h3>
        <p class="mt-2 text-sm text-gray-500 dark:text-gray-400">For organizations with advanced needs.</p>

        <p class="mt-6">
          <span class="text-4xl font-bold text-gray-900 dark:text-white">Custom</span>
        </p>

        <a href="#" class="mt-8 block rounded-lg border border-gray-300 py-2.5 text-center text-sm font-semibold text-gray-700 hover:bg-gray-50 transition-colors dark:border-gray-700 dark:text-gray-300 dark:hover:bg-gray-900">
          Contact sales
        </a>

        <ul class="mt-8 space-y-3 text-sm text-gray-600 dark:text-gray-400">
          <li class="flex items-start gap-3">
            <svg class="mt-0.5 h-5 w-5 shrink-0 text-indigo-500" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" /></svg>
            Everything in Pro
          </li>
          <li class="flex items-start gap-3">
            <svg class="mt-0.5 h-5 w-5 shrink-0 text-indigo-500" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" /></svg>
            Unlimited storage
          </li>
          <li class="flex items-start gap-3">
            <svg class="mt-0.5 h-5 w-5 shrink-0 text-indigo-500" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" /></svg>
            Dedicated account manager
          </li>
          <li class="flex items-start gap-3">
            <svg class="mt-0.5 h-5 w-5 shrink-0 text-indigo-500" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" /></svg>
            SSO &amp; SAML
          </li>
          <li class="flex items-start gap-3">
            <svg class="mt-0.5 h-5 w-5 shrink-0 text-indigo-500" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" /></svg>
            99.99% SLA
          </li>
        </ul>
      </div>

    </div>
  </div>
</section>
```

## Comparison Table

```html
<section class="bg-gray-50 py-20 dark:bg-gray-900 sm:py-28">
  <div class="mx-auto max-w-5xl px-6">
    <h2 class="text-center text-3xl font-bold tracking-tight text-gray-900 dark:text-white sm:text-4xl">
      Compare plans
    </h2>

    <div class="mt-12 overflow-x-auto">
      <table class="w-full text-left text-sm">
        <thead>
          <tr class="border-b border-gray-200 dark:border-gray-700">
            <th class="pb-4 pr-6 font-medium text-gray-500 dark:text-gray-400">Feature</th>
            <th class="pb-4 px-6 text-center font-semibold text-gray-900 dark:text-white">Starter</th>
            <th class="pb-4 px-6 text-center font-semibold text-indigo-600 dark:text-indigo-400">Pro</th>
            <th class="pb-4 pl-6 text-center font-semibold text-gray-900 dark:text-white">Enterprise</th>
          </tr>
        </thead>
        <tbody class="divide-y divide-gray-200 dark:divide-gray-800">
          <!-- Row -->
          <tr>
            <td class="py-4 pr-6 text-gray-700 dark:text-gray-300">Projects</td>
            <td class="py-4 px-6 text-center text-gray-600 dark:text-gray-400">3</td>
            <td class="py-4 px-6 text-center font-medium text-gray-900 dark:text-white">Unlimited</td>
            <td class="py-4 pl-6 text-center text-gray-600 dark:text-gray-400">Unlimited</td>
          </tr>
          <tr>
            <td class="py-4 pr-6 text-gray-700 dark:text-gray-300">Storage</td>
            <td class="py-4 px-6 text-center text-gray-600 dark:text-gray-400">1 GB</td>
            <td class="py-4 px-6 text-center font-medium text-gray-900 dark:text-white">50 GB</td>
            <td class="py-4 pl-6 text-center text-gray-600 dark:text-gray-400">Unlimited</td>
          </tr>
          <tr>
            <td class="py-4 pr-6 text-gray-700 dark:text-gray-300">Team members</td>
            <td class="py-4 px-6 text-center text-gray-600 dark:text-gray-400">1</td>
            <td class="py-4 px-6 text-center font-medium text-gray-900 dark:text-white">10</td>
            <td class="py-4 pl-6 text-center text-gray-600 dark:text-gray-400">Unlimited</td>
          </tr>
          <tr>
            <td class="py-4 pr-6 text-gray-700 dark:text-gray-300">Analytics</td>
            <td class="py-4 px-6 text-center text-gray-400">
              <svg class="mx-auto h-5 w-5" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" /></svg>
              <span class="sr-only">Not included</span>
            </td>
            <td class="py-4 px-6 text-center text-indigo-500">
              <svg class="mx-auto h-5 w-5" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" /></svg>
              <span class="sr-only">Included</span>
            </td>
            <td class="py-4 pl-6 text-center text-indigo-500">
              <svg class="mx-auto h-5 w-5" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" /></svg>
              <span class="sr-only">Included</span>
            </td>
          </tr>
          <tr>
            <td class="py-4 pr-6 text-gray-700 dark:text-gray-300">SSO / SAML</td>
            <td class="py-4 px-6 text-center text-gray-400">
              <svg class="mx-auto h-5 w-5" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" /></svg>
              <span class="sr-only">Not included</span>
            </td>
            <td class="py-4 px-6 text-center text-gray-400">
              <svg class="mx-auto h-5 w-5" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" /></svg>
              <span class="sr-only">Not included</span>
            </td>
            <td class="py-4 pl-6 text-center text-indigo-500">
              <svg class="mx-auto h-5 w-5" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" /></svg>
              <span class="sr-only">Included</span>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</section>
```

## Feature List with Tooltips

```html
<ul class="space-y-3 text-sm text-gray-600 dark:text-gray-400">
  <li class="flex items-start gap-3">
    <svg class="mt-0.5 h-5 w-5 shrink-0 text-indigo-500" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" /></svg>
    <span>
      Advanced analytics
      <button type="button" class="group relative ml-1 inline-flex" aria-label="More info about advanced analytics">
        <svg class="h-4 w-4 text-gray-400 hover:text-gray-600 dark:hover:text-gray-300 transition-colors" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M9.879 7.519c1.171-1.025 3.071-1.025 4.242 0 1.172 1.025 1.172 2.687 0 3.712-.203.179-.43.326-.67.442-.745.361-1.45.999-1.45 1.827v.75M12 18.75h.008v.008H12v-.008z" /></svg>
        <span role="tooltip" class="pointer-events-none absolute bottom-full left-1/2 z-10 mb-2 -translate-x-1/2 whitespace-nowrap rounded-lg bg-gray-900 px-3 py-2 text-xs text-white opacity-0 shadow-lg transition-opacity group-hover:opacity-100 group-focus:opacity-100 dark:bg-gray-700">
          Includes funnel analysis, cohort reports, and custom dashboards.
        </span>
      </button>
    </span>
  </li>
</ul>
```

## CTA Button Variants

```html
<!-- Primary — recommended plan -->
<a href="#" class="block w-full rounded-lg bg-indigo-600 py-3 text-center text-sm font-semibold text-white shadow-md hover:bg-indigo-500 focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600 transition-colors">
  Start free trial
</a>

<!-- Secondary — standard plan -->
<a href="#" class="block w-full rounded-lg border border-gray-300 py-3 text-center text-sm font-semibold text-gray-700 hover:bg-gray-50 focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600 transition-colors dark:border-gray-700 dark:text-gray-300 dark:hover:bg-gray-800">
  Get started
</a>

<!-- Enterprise — contact sales -->
<a href="#" class="block w-full rounded-lg bg-gray-900 py-3 text-center text-sm font-semibold text-white hover:bg-gray-800 transition-colors dark:bg-white dark:text-gray-900 dark:hover:bg-gray-200">
  Contact sales
</a>
```

## Trust Signals

```html
<div class="mt-16 flex flex-col items-center gap-6 text-center">
  <!-- Money-back guarantee badge -->
  <div class="flex items-center gap-2 rounded-full bg-green-50 px-4 py-2 text-sm font-medium text-green-700 ring-1 ring-green-600/20 ring-inset dark:bg-green-900/30 dark:text-green-400 dark:ring-green-500/30">
    <svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor">
      <path stroke-linecap="round" stroke-linejoin="round" d="M9 12.75L11.25 15 15 9.75m-3-7.036A11.959 11.959 0 013.598 6 11.99 11.99 0 003 9.749c0 5.592 3.824 10.29 9 11.623 5.176-1.332 9-6.03 9-11.622 0-1.31-.21-2.571-.598-3.751h-.152c-3.196 0-6.1-1.248-8.25-3.285z" />
    </svg>
    30-day money-back guarantee
  </div>
  <p class="text-sm text-gray-500 dark:text-gray-400">
    Cancel anytime. No questions asked.
  </p>
</div>
```

## Responsive Mobile Stacking

Pricing cards automatically stack on mobile with the `lg:grid-cols-3` class. Additional mobile considerations:

- Use `overflow-x-auto` on comparison tables to enable horizontal scrolling.
- Collapse feature lists to show only 3–4 items with a "See all features" toggle.
- Ensure the recommended plan card appears first on mobile (use `order-first lg:order-none` on the popular plan).
- Make billing toggle sticky on scroll (`sticky top-16 z-10 bg-white/80 backdrop-blur`).

```html
<!-- Recommended plan appears first on mobile -->
<div class="mt-12 grid gap-8 lg:grid-cols-3">
  <div class="order-first lg:order-none relative rounded-2xl border-2 border-indigo-600 p-8 shadow-lg">
    <!-- Pro plan content (recommended) -->
  </div>
  <div class="rounded-2xl border border-gray-200 p-8 lg:order-first">
    <!-- Starter plan content -->
  </div>
  <div class="rounded-2xl border border-gray-200 p-8">
    <!-- Enterprise plan content -->
  </div>
</div>
```

## Best Practices

- **Anchor pricing**: put the highest-value plan in the center to anchor perception.
- **Show savings**: display annual discount as a percentage badge near the toggle.
- **Limit tiers**: 3 plans is optimal; 4+ causes decision paralysis.
- **Clear differentiation**: each tier should have a distinct target audience.
- **Social proof near CTA**: place a short testimonial or user count near the sign-up button.
- **Transparent pricing**: always show the actual price — avoid "starting at" when possible.
- **Accessible toggle**: use `role="switch"` and `aria-checked` for the billing toggle.

## Anti-Patterns

- **Too many tiers** — more than 4 cards overwhelm users.
- **Identical feature lists** — tiers should feel meaningfully different.
- **No visual hierarchy** — the recommended plan must stand out immediately.
- **Price per seat only** — show the total cost, not just per-seat, to avoid sticker shock.
- **Hidden limitations** — document rate limits, storage caps, and usage overages clearly.
- **No annual discount** — annual billing stabilizes revenue and should always be offered.
- **CTA says "Buy now"** — use trial-oriented copy ("Start free trial", "Get started") to lower friction.
