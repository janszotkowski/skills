---
name: tanstack-start-marketing-page-seo
description: Ensure consistent SEO and structured data on marketing/informational frontend pages (landing, about, contact, other content pages) in TanStack Start apps, including titles, meta descriptions, canonical URLs, JSON-LD, and breadcrumbs.
tags: [seo, tanstack-start, frontend, web]
scope: agent-guidance
version: 0.1.0
status: draft
---

## Goal

Provide a consistent pattern for **SEO metadata** and **structured data** on **marketing and informational pages** in TanStack Start applications, while staying decoupled from any specific app’s copy, design system, or translation library.

## When to use

- When working on marketing / informational routes such as:
  - Landing pages (`/`)
  - Marketing/info pages (`/about`, `/contact`, `/pricing`, `/features`, etc.)
- When editing the **route `head` configuration** (title, meta description, canonical link).
- When adding or updating **JSON-LD structured data** for:
  - `WebSite`, `WebPage`, `Organization`,
  - `FAQPage` (for FAQ-style pages),
  - `BreadcrumbList` for hierarchical navigation.
- When deciding **indexability** for marketing vs. utility/private pages.

## Rules / guidelines

### 1. Route paths vs. canonical URLs

- TanStack Start file routes can live under any internal prefix, but **SEO must always use the final canonical URL**:
  - Internal route file (example): `createFileRoute('/_internal/about')`.
  - Canonical URL used in SEO/meta: `'/about'`.
- Never leak internal route prefixes (like `/_public`, `/app`, `/__internal`) into:
  - `canonical` URLs,
  - Open Graph / Twitter URL fields,
  - JSON-LD `@id` or `url` fields,
  - Breadcrumb `item` URLs.

### 2. Recommended helpers (framework-agnostic)

In TanStack Start apps, prefer central helpers/utilities for SEO instead of inlining meta logic in every route. A typical pattern is:

- **Site config**
  - `siteConfig.title`
  - `siteConfig.description`
  - `siteConfig.url` (origin, e.g. `https://example.com`)
  - `siteConfig.defaultOgImage`

- **Canonical & meta**
  - `canonicalUrl(path: string): string` → returns an absolute, HTTPS URL with normalized slashes.
  - `canonicalLink(path: string)` → returns `{ rel: 'canonical', href: string }` for `<link>` elements.
  - `buildPageMeta({ title, description, path, image? })` → returns a standard set of `<meta>` tags (OG, Twitter, etc.) for a given page.

- **JSON-LD (global & per-page)**
  - `getJsonLdOrganization()`
  - `getJsonLdWebSite(options)` – e.g. `{ inLanguage?: string }`
  - `getJsonLdWebPage({ name, description, path, inLanguage? })`
  - `getJsonLdBreadcrumbList(items: BreadcrumbListItem[])`

Where `BreadcrumbListItem` follows:

```ts
type BreadcrumbListItem = {
  position: number;
  name: string;
  item: string; // absolute URL, usually from canonicalUrl()
};
```

If your project doesn’t have these helpers yet, implement them once in a shared `seo` utility module and reuse them in all marketing/information routes.

### 3. Head pattern for marketing pages

For any marketing or informational route (landing, about, contact, pricing, features, etc.), follow this pattern inside the route definition:

1. **Derive title and description** from your project’s translation or copy system (e.g. i18n library, message catalog, constants). Avoid hard-coding raw strings directly in the route.
2. **Use the canonical path** for SEO (e.g. `'/about'`, `'/contact'`, `'/pricing'`).

Example (framework-agnostic TypeScript-style snippet):

```ts
const title = t('about.title'); // or any translation / copy lookup
const description = t('about.metaDescription');
const locale = getLocale(); // project-specific
const path = '/about';

const pageMeta = buildPageMeta({ title, description, path });
const webPage = getJsonLdWebPage({
  name: title,
  description,
  path,
  inLanguage: locale,
});
const webSite = getJsonLdWebSite({ inLanguage: locale });
const organization = getJsonLdOrganization();
```

Then return the `head` object for the TanStack Start route:

```ts
head: () => ({
  title,
  meta: pageMeta,
  links: [canonicalLink(path)],
  scripts: [
    { type: 'application/ld+json', children: JSON.stringify(organization) },
    { type: 'application/ld+json', children: JSON.stringify(webSite) },
    { type: 'application/ld+json', children: JSON.stringify(webPage) },
  ],
});
```

Guidelines:

- `title` must be **unique and descriptive** for each marketing / informational page.
- `description` must reflect the actual visible content.
- `path` must always be the **public URL**, not the internal file route.

### 4. Breadcrumbs for hierarchical pages

For public pages that are part of a simple hierarchy (e.g. `Home → About`, `Home → Contact`, `Home → Pricing`), include `BreadcrumbList` JSON-LD via the breadcrumb helper.

Example:

```ts
const breadcrumbsJsonLd = getJsonLdBreadcrumbList([
  {
    position: 1,
    name: t('nav.home'),
    item: canonicalUrl('/'),
  },
  {
    position: 2,
    name: title,
    item: canonicalUrl(path),
  },
]);
```

Add it to the route `head`:

```ts
scripts: [
  { type: 'application/ld+json', children: JSON.stringify(organization) },
  { type: 'application/ld+json', children: JSON.stringify(webSite) },
  { type: 'application/ld+json', children: JSON.stringify(webPage) },
  { type: 'application/ld+json', children: JSON.stringify(breadcrumbsJsonLd) },
];
```

Breadcrumb rules:

- The “Home” item:
  - `name`: use the same label as in the main navigation (via translations / config).
  - `item`: `canonicalUrl('/')`.
- The current page:
  - `position`: next number after Home (usually 2).
  - `name`: the page title or dedicated label.
  - `item`: `canonicalUrl(path)` matching the public URL.

### 5. FAQ-style pages (`FAQPage` JSON-LD)

For contact or FAQ pages that have a question–answer pattern, consider adding `FAQPage` structured data.

Example (generic structure):

```ts
const faqItems = [
  { q: t('faq.paymentQuestion'), a: t('faq.paymentAnswer') },
  { q: t('faq.shippingQuestion'), a: t('faq.shippingAnswer') },
];

const faqJsonLd = {
  '@context': 'https://schema.org',
  '@type': 'FAQPage',
  mainEntity: faqItems.map((item) => ({
    '@type': 'Question',
    name: item.q,
    acceptedAnswer: {
      '@type': 'Answer',
      text: item.a,
    },
  })),
};
```

Include it in the `head.scripts` array along with other JSON-LD objects.

### 6. Indexability (robots meta) for marketing vs. private routes

- **Marketing/info pages**: usually should be indexable. Rely on global defaults (`index, follow`) unless there is a specific reason not to.
- **Utility, auth, dashboard, or backoffice pages**: often should **not** be indexed. This skill does not govern those routes, but for context:
  - Add a `robots` meta tag like:

```ts
meta: [
  ...buildPageMeta({ title, description, path }),
  { name: 'robots', content: 'noindex, nofollow' },
];
```

Keep indexability decisions explicit and consistent.

## Checklist

Before finalizing changes to a **TanStack Start marketing route**, verify:

- [ ] The route uses the final **canonical URL** in SEO fields (no internal prefixes).
- [ ] Page title is unique, descriptive, and comes from your copy/translation system.
- [ ] Meta description is present, honest, and matches the page content.
- [ ] `buildPageMeta` (or equivalent utility) is used with the correct `path`.
- [ ] `canonicalLink(path)` is included in the `links` array.
- [ ] `getJsonLdWebPage`, `getJsonLdWebSite`, and `getJsonLdOrganization` (or equivalent) are included in `scripts` for key public pages.
- [ ] Breadcrumbs via `getJsonLdBreadcrumbList` are added where hierarchical navigation exists.
- [ ] FAQ-style pages use `FAQPage` JSON-LD when appropriate.
- [ ] Private/utility routes that should not appear in search results use `noindex, nofollow` (if relevant to the change).

## Examples (optional)

- Adapt the patterns in this skill to your own:
  - translation/copy system,
  - `seo` utility module,
  - route file structure.
- Keep all SEO logic in shared helpers where possible and make route-level `head` implementations thin and declarative.

