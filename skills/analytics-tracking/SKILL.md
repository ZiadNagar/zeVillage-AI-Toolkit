---
name: analytics-tracking
description: "Use this skill when implementing analytics and tracking — GA4 setup, event taxonomy, conversion tracking, Google Tag Manager, dataLayer, UTM strategies, consent management, server-side tracking, and debugging with DebugView."
license: Complete terms in LICENSE.txt
---

# Analytics & Tracking Implementation

## When to Use

- Setting up GA4 on a new website or application.
- Designing an event taxonomy with consistent naming conventions.
- Implementing conversion tracking and enhanced ecommerce events.
- Configuring Google Tag Manager and the dataLayer.
- Building UTM parameter strategies for campaign attribution.
- Implementing cookie consent for GDPR/CCPA compliance.
- Debugging tracking with GA4 DebugView or GTM preview mode.
- Defining KPIs and designing dashboards.

## GA4 Setup & Configuration

### Base Installation

Add the GA4 snippet to the `<head>` of every page, or deploy via Google Tag Manager:

```html
<!-- Direct GA4 installation -->
<script
  async
  src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"
></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag() {
    dataLayer.push(arguments);
  }
  gtag("js", new Date());
  gtag("config", "G-XXXXXXXXXX", {
    send_page_view: true,
    cookie_flags: "SameSite=None;Secure",
  });
</script>
```

### GTM-Based Installation (Recommended)

```html
<!-- Google Tag Manager -->
<script>
  (function (w, d, s, l, i) {
    w[l] = w[l] || [];
    w[l].push({ "gtm.start": new Date().getTime(), event: "gtm.js" });
    var f = d.getElementsByTagName(s)[0],
      j = d.createElement(s),
      dl = l != "dataLayer" ? "&l=" + l : "";
    j.async = true;
    j.src = "https://www.googletagmanager.com/gtm.js?id=" + i + dl;
    f.parentNode.insertBefore(j, f);
  })(window, document, "script", "dataLayer", "GTM-XXXXXXX");
</script>

<!-- GTM noscript fallback (immediately after <body>) -->
<noscript>
  <iframe
    src="https://www.googletagmanager.com/ns.html?id=GTM-XXXXXXX"
    height="0"
    width="0"
    style="display:none;visibility:hidden"
  ></iframe>
</noscript>
```

### Configuration Checklist

- [ ] Enable enhanced measurement (scrolls, outbound clicks, site search, file downloads).
- [ ] Set up cross-domain tracking if the product spans multiple domains.
- [ ] Configure data retention period (14 months for standard, 2 months default).
- [ ] Link Google Search Console and Google Ads.
- [ ] Set internal traffic filters (exclude office IPs and developer traffic).
- [ ] Enable Google Signals for cross-device reporting (where consent allows).
- [ ] Create custom channel groupings if defaults don't match your funnel.

## Event Taxonomy Design

### Naming Conventions

Use a consistent `object_action` pattern with snake_case:

```
Format: {object}_{action}

Examples:
  page_view          (auto-collected)
  button_click
  form_submit
  video_play
  signup_start
  signup_complete
  plan_select
  checkout_begin
  purchase_complete
  feature_activate
  error_encounter
```

### Event Hierarchy

```
Category 1: Navigation
  ├── page_view
  ├── tab_switch
  └── menu_open

Category 2: Engagement
  ├── button_click
  ├── link_click
  ├── video_play
  ├── video_complete
  ├── scroll_depth (25%, 50%, 75%, 100%)
  └── file_download

Category 3: Conversion
  ├── signup_start
  ├── signup_complete
  ├── trial_start
  ├── checkout_begin
  ├── purchase_complete
  └── plan_upgrade

Category 4: Product Usage
  ├── feature_activate
  ├── project_create
  ├── invite_send
  ├── integration_connect
  └── export_complete

Category 5: Errors
  ├── error_encounter
  ├── form_validation_fail
  └── payment_fail
```

### Event Parameters

Every custom event should include relevant parameters:

```javascript
// Good: rich parameters
gtag("event", "signup_complete", {
  method: "google_oauth",
  plan_selected: "pro",
  referral_source: "blog_post",
  time_to_complete_seconds: 34,
});

// Bad: no context
gtag("event", "signup_complete");
```

### Standard Parameter Names

| Parameter      | Type   | Description                                          |
| -------------- | ------ | ---------------------------------------------------- |
| `method`       | string | How the action was performed (google, email, github) |
| `content_type` | string | Type of content (blog, docs, video)                  |
| `item_id`      | string | Unique identifier of the item                        |
| `plan_name`    | string | Name of the selected plan                            |
| `value`        | number | Monetary value associated with the event             |
| `currency`     | string | ISO 4217 currency code (USD, EUR)                    |
| `source`       | string | Internal attribution source                          |

## Conversion Tracking

### Defining Conversions

Mark key events as conversions in GA4 Admin > Events > Mark as conversion:

```
Primary Conversions:
  ✓ signup_complete
  ✓ purchase_complete
  ✓ trial_start

Secondary Conversions:
  ✓ demo_request
  ✓ contact_form_submit
  ✓ plan_upgrade
```

### Conversion Event Pattern

```javascript
// Purchase conversion with full ecommerce data
gtag("event", "purchase", {
  transaction_id: "TXN_12345",
  value: 79.0,
  currency: "USD",
  tax: 6.32,
  items: [
    {
      item_id: "plan_pro",
      item_name: "Pro Plan",
      item_category: "subscription",
      price: 79.0,
      quantity: 1,
    },
  ],
});
```

### Conversion Funnel Definition

```
Awareness:  page_view (landing page)
Interest:   cta_click, pricing_view
Desire:     signup_start, trial_start
Action:     purchase_complete

Drop-off points to monitor:
  Landing → Signup Start:     target > 5%
  Signup Start → Complete:    target > 70%
  Trial Start → Purchase:    target > 15%
```

## Enhanced Ecommerce Events

### Full Ecommerce Event Flow

```javascript
// 1. View item list (e.g., pricing page loads)
gtag("event", "view_item_list", {
  item_list_id: "pricing_plans",
  item_list_name: "Pricing Plans",
  items: [
    { item_id: "plan_starter", item_name: "Starter", price: 29.0 },
    { item_id: "plan_pro", item_name: "Pro", price: 79.0 },
    { item_id: "plan_enterprise", item_name: "Enterprise", price: 299.0 },
  ],
});

// 2. Select item (user clicks a plan)
gtag("event", "select_item", {
  item_list_id: "pricing_plans",
  items: [{ item_id: "plan_pro", item_name: "Pro", price: 79.0 }],
});

// 3. Begin checkout
gtag("event", "begin_checkout", {
  currency: "USD",
  value: 79.0,
  items: [{ item_id: "plan_pro", item_name: "Pro", price: 79.0, quantity: 1 }],
});

// 4. Add payment info
gtag("event", "add_payment_info", {
  currency: "USD",
  value: 79.0,
  payment_type: "credit_card",
});

// 5. Purchase
gtag("event", "purchase", {
  transaction_id: "TXN_" + Date.now(),
  value: 79.0,
  currency: "USD",
  items: [{ item_id: "plan_pro", item_name: "Pro", price: 79.0, quantity: 1 }],
});
```

## Google Tag Manager & dataLayer

### dataLayer Initialization

Always initialize the dataLayer before the GTM snippet:

```html
<script>
  window.dataLayer = window.dataLayer || [];
</script>
<!-- GTM snippet follows -->
```

### dataLayer Push Patterns

```javascript
// Page context (push on every page load)
window.dataLayer.push({
  event: "page_metadata",
  page_type: "pricing",
  user_logged_in: true,
  user_plan: "free",
  user_id_hashed: "a1b2c3d4e5", // never push raw PII
});

// Custom event
window.dataLayer.push({
  event: "signup_complete",
  method: "email",
  plan_selected: "pro",
});

// Ecommerce event
window.dataLayer.push({
  event: "purchase",
  ecommerce: {
    transaction_id: "TXN_12345",
    value: 79.0,
    currency: "USD",
    items: [
      {
        item_id: "plan_pro",
        item_name: "Pro Plan",
        price: 79.0,
        quantity: 1,
      },
    ],
  },
});

// Clear ecommerce object before pushing new ecommerce data
window.dataLayer.push({ ecommerce: null });
```

### GTM Trigger Naming Convention

```
Format: {event_type} - {description}

Examples:
  CE - Signup Complete           (Custom Event)
  CE - Purchase                  (Custom Event)
  PV - Pricing Page              (Page View)
  CL - CTA Button Click         (Click)
  TM - 30 Second Timer          (Timer)
  EL - DOM Ready                (Element Visibility)
```

### GTM Variable Patterns

Use Data Layer Variables to extract values from dataLayer pushes:

```
Variable Name:     dlv - plan_selected
Variable Type:     Data Layer Variable
Data Layer Name:   plan_selected
Data Layer Version: Version 2
```

## UTM Parameter Strategy

### UTM Parameter Reference

| Parameter      | Required | Purpose                  | Example                      |
| -------------- | -------- | ------------------------ | ---------------------------- |
| `utm_source`   | Yes      | Traffic source           | google, newsletter, twitter  |
| `utm_medium`   | Yes      | Marketing medium         | cpc, email, social, referral |
| `utm_campaign` | Yes      | Campaign name            | spring_launch, black_friday  |
| `utm_term`     | No       | Paid search keyword      | project_management_tool      |
| `utm_content`  | No       | Differentiates creatives | hero_cta_v2, sidebar_banner  |

### UTM Naming Convention

```
Pattern: {source}_{medium}_{campaign}_{content}

All lowercase, underscores, no spaces:
  ✓ utm_source=google&utm_medium=cpc&utm_campaign=brand_2026_q1
  ✗ utm_source=Google&utm_medium=CPC&utm_campaign=Brand 2026 Q1
```

### UTM Parsing Utility

```javascript
/**
 * Parse UTM parameters from the current URL and store in sessionStorage.
 * Call on page load to preserve attribution across the session.
 */
function captureUTMParams() {
  const params = new URLSearchParams(window.location.search);
  const utmKeys = [
    "utm_source",
    "utm_medium",
    "utm_campaign",
    "utm_term",
    "utm_content",
  ];
  const utmData = {};

  utmKeys.forEach((key) => {
    const value = params.get(key);
    if (value) utmData[key] = value.toLowerCase().trim();
  });

  if (Object.keys(utmData).length > 0) {
    sessionStorage.setItem("utm_data", JSON.stringify(utmData));

    // Push to dataLayer for GTM
    window.dataLayer = window.dataLayer || [];
    window.dataLayer.push({ event: "utm_captured", ...utmData });
  }
}

/**
 * Retrieve stored UTM data for form submissions or conversion events.
 */
function getUTMParams() {
  const stored = sessionStorage.getItem("utm_data");
  return stored ? JSON.parse(stored) : {};
}

// Attach UTM data to signup forms
function attachUTMToForm(formElement) {
  const utmData = getUTMParams();
  Object.entries(utmData).forEach(([key, value]) => {
    const input = document.createElement("input");
    input.type = "hidden";
    input.name = key;
    input.value = value;
    formElement.appendChild(input);
  });
}
```

## Attribution Models

| Model          | Description                                     | Best For                                        |
| -------------- | ----------------------------------------------- | ----------------------------------------------- |
| Last Click     | 100% credit to last touchpoint                  | Simple, direct-response campaigns               |
| First Click    | 100% credit to first touchpoint                 | Understanding top-of-funnel sources             |
| Linear         | Equal credit across all touchpoints             | Balanced multi-channel view                     |
| Time Decay     | More credit to touchpoints closer to conversion | Long sales cycles                               |
| Position-Based | 40% first, 40% last, 20% split middle           | Balanced first/last emphasis                    |
| Data-Driven    | Algorithmic based on actual conversion paths    | Sufficient data volume (300+ conversions/month) |

**GA4 default:** Data-driven attribution (where data volume permits), otherwise cross-channel last-click.

## Consent Management

### Cookie Consent Implementation

```javascript
// Consent state management
const CONSENT_KEY = "cookie_consent";

function getConsentState() {
  const stored = localStorage.getItem(CONSENT_KEY);
  return stored ? JSON.parse(stored) : null;
}

function setConsentState(consent) {
  localStorage.setItem(
    CONSENT_KEY,
    JSON.stringify({
      analytics: consent.analytics || false,
      marketing: consent.marketing || false,
      timestamp: new Date().toISOString(),
      version: "1.0",
    }),
  );

  applyConsent(consent);
}

function applyConsent(consent) {
  // Update GA4 consent mode
  gtag("consent", "update", {
    analytics_storage: consent.analytics ? "granted" : "denied",
    ad_storage: consent.marketing ? "granted" : "denied",
    ad_user_data: consent.marketing ? "granted" : "denied",
    ad_personalization: consent.marketing ? "granted" : "denied",
  });

  // Push consent event to dataLayer
  window.dataLayer.push({
    event: "consent_update",
    consent_analytics: consent.analytics,
    consent_marketing: consent.marketing,
  });
}

// Initialize with default denied state (GDPR-safe)
gtag("consent", "default", {
  analytics_storage: "denied",
  ad_storage: "denied",
  ad_user_data: "denied",
  ad_personalization: "denied",
  wait_for_update: 500, // wait 500ms for consent tool
});
```

### GDPR vs. CCPA Requirements

| Aspect                           | GDPR (EU/EEA)              | CCPA (California)                       |
| -------------------------------- | -------------------------- | --------------------------------------- |
| Default state                    | Opt-in required            | Opt-out available                       |
| Consent required before tracking | Yes                        | No (but must honor opt-out)             |
| Cookie banner                    | Required                   | "Do Not Sell" link required             |
| Data deletion requests           | Must comply within 30 days | Must comply within 45 days              |
| Applies to                       | All EU users               | CA residents, businesses > $25M revenue |

### Consent Banner HTML Pattern

```html
<div
  id="cookie-banner"
  role="dialog"
  aria-label="Cookie consent"
  class="fixed bottom-0 inset-x-0 z-50 bg-white border-t p-4 shadow-lg"
>
  <div
    class="max-w-4xl mx-auto flex flex-col sm:flex-row items-start sm:items-center gap-4"
  >
    <p class="text-sm text-gray-700 flex-1">
      We use cookies to analyze site usage and improve your experience.
      <a href="/privacy" class="underline">Privacy policy</a>.
    </p>
    <div class="flex gap-2">
      <button
        onclick="setConsentState({analytics:false,marketing:false})"
        class="px-4 py-2 text-sm border rounded hover:bg-gray-50"
      >
        Reject All
      </button>
      <button
        onclick="setConsentState({analytics:true,marketing:false})"
        class="px-4 py-2 text-sm border rounded hover:bg-gray-50"
      >
        Analytics Only
      </button>
      <button
        onclick="setConsentState({analytics:true,marketing:true})"
        class="px-4 py-2 text-sm bg-gray-900 text-white rounded hover:bg-gray-800"
      >
        Accept All
      </button>
    </div>
  </div>
</div>
```

## Server-Side Tracking Basics

### Why Server-Side

- **Ad blockers** can't block server-side events.
- **Data accuracy** — no browser-level interference.
- **PII control** — filter sensitive data before it reaches analytics platforms.
- **First-party data** — events come from your domain.

### Server-Side Architecture

```
Browser → Your Server → Measurement Protocol → GA4
                      → Meta Conversions API
                      → Other platforms
```

### GA4 Measurement Protocol Example

```javascript
// Server-side: send event to GA4 via Measurement Protocol
async function sendServerEvent(clientId, events) {
  const measurementId = "G-XXXXXXXXXX";
  const apiSecret = process.env.GA4_API_SECRET;

  const response = await fetch(
    `https://www.google-analytics.com/mp/collect?measurement_id=${measurementId}&api_secret=${apiSecret}`,
    {
      method: "POST",
      body: JSON.stringify({
        client_id: clientId,
        events: events,
      }),
    },
  );

  return response.status === 204; // 204 = success
}

// Usage
await sendServerEvent("client_id_from_cookie", [
  {
    name: "purchase",
    params: {
      transaction_id: "TXN_12345",
      value: 79.0,
      currency: "USD",
      items: [
        { item_id: "plan_pro", item_name: "Pro", price: 79.0, quantity: 1 },
      ],
    },
  },
]);
```

## Debugging

### GA4 DebugView

1. Enable debug mode by adding the `debug_mode` parameter:

```javascript
gtag("config", "G-XXXXXXXXXX", { debug_mode: true });
```

2. Or install the [GA Debugger browser extension](https://chrome.google.com/webstore/detail/google-analytics-debugger).
3. Open GA4 Admin > DebugView to see events in real time.
4. Verify event names, parameters, and user properties.

### GTM Preview Mode

1. Click "Preview" in GTM workspace.
2. Enter the site URL — Tag Assistant opens in a new tab.
3. Interact with the site and verify:
   - Which tags fired on each event.
   - Which triggers matched.
   - What data was in the dataLayer at each step.
4. Check the "Data Layer" tab for each event to verify parameter values.

### Console Debugging Pattern

```javascript
// Temporary: log all dataLayer pushes to console
(function () {
  const originalPush = window.dataLayer.push;
  window.dataLayer.push = function () {
    console.group("dataLayer.push");
    console.log(JSON.stringify(arguments[0], null, 2));
    console.groupEnd();
    return originalPush.apply(this, arguments);
  };
})();
```

### Common Debugging Checklist

- [ ] Events appear in GA4 DebugView within 5 seconds.
- [ ] Event names follow the naming convention (snake_case, object_action).
- [ ] Required parameters are present and non-null.
- [ ] Currency values use ISO 4217 codes.
- [ ] Transaction IDs are unique per purchase.
- [ ] Consent mode is set to `denied` by default, updated on user action.
- [ ] No PII (emails, names, phone numbers) in event parameters.
- [ ] Ecommerce events fire in the correct sequence.
- [ ] Page view events fire once per navigation (not double-firing on SPAs).

## KPI Definition Framework

### KPI Hierarchy

```
North Star Metric
  └── Primary KPIs (3–5)
        └── Supporting Metrics (per KPI)
              └── Diagnostic Metrics (for debugging)
```

### Example: SaaS KPI Framework

| Level      | Metric                               | Target                | Source            |
| ---------- | ------------------------------------ | --------------------- | ----------------- |
| North Star | Monthly Recurring Revenue (MRR)      | +10% MoM              | Billing system    |
| Primary    | Trial-to-Paid Conversion             | > 15%                 | GA4 conversion    |
| Primary    | Activation Rate (first value moment) | > 60% within 24h      | Product analytics |
| Primary    | Net Revenue Retention                | > 110%                | Billing system    |
| Supporting | Signup-to-Trial Rate                 | > 8%                  | GA4 funnel        |
| Supporting | Feature Adoption (core feature)      | > 70% of active users | Product analytics |
| Diagnostic | Page Load Time                       | < 2s                  | Web Vitals        |
| Diagnostic | Error Rate                           | < 0.5%                | Error tracking    |

### Dashboard Design Principles

1. **One metric per card** — no multi-stat cards.
2. **Trend lines over snapshots** — always show change over time.
3. **Comparison context** — show vs. previous period, vs. target, vs. benchmark.
4. **Action-oriented** — every metric should answer "what should I do next?"
5. **Limit to 6–8 metrics per dashboard** — create separate views for deep dives.

## Best Practices

1. **Plan before you implement** — define your event taxonomy and KPIs before writing any tracking code.
2. **Use GTM for all tracking** — avoid hardcoding analytics snippets in source code.
3. **Never send PII to analytics** — hash user IDs, strip emails, mask IP addresses.
4. **Test in staging first** — use GTM environments and GA4 DebugView before pushing to production.
5. **Document everything** — maintain a tracking plan spreadsheet with event names, parameters, triggers, and owners.
6. **Set up alerts** — create custom alerts for conversion drop-offs, traffic anomalies, and error spikes.
7. **Audit quarterly** — verify that events still fire correctly after product changes.
8. **Separate dev and prod** — use different GA4 properties and GTM containers for development and production.

## Anti-Patterns

- **Tracking everything** — more events ≠ more insights. Track what drives decisions.
- **Inconsistent naming** — mixing `camelCase`, `kebab-case`, and `snake_case` makes analysis painful.
- **Skipping consent** — non-compliance leads to legal penalties and user trust erosion.
- **Hardcoded tracking** — embedding gtag calls throughout the codebase makes maintenance a nightmare. Use GTM.
- **Ignoring ad blockers** — 25–40% of tech-savvy users block analytics. Consider server-side for critical events.
- **No data validation** — garbage data in, garbage insights out. Validate event parameters before sending.
- **Vanity metrics** — total pageviews and registered users feel good but rarely drive decisions. Focus on activation and retention.
- **Missing attribution** — launching campaigns without UTM parameters makes ROI impossible to calculate.
