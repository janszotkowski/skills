---
name: seo
description: Optimize for search engine visibility and ranking through technical SEO, on-page optimization, and structured data. Use when asked to improve SEO, meta tags, structured data, sitemaps, or search engine visibility.
tags: [seo, frontend, web]
scope: agent-guidance
version: 1.0.0
status: stable
---

## Goal

Help the agent implement **technical SEO** and **on-page SEO** that aligns with Lighthouse SEO audits and Google Search guidelines, with a strong focus on crawlability, indexability, structured data, and on-page signals.

## When to use

- When asked to **"improve SEO"**, **"optimize for search"**, or **"fix SEO issues"**.
- When working on **meta tags**, **titles**, **descriptions**, **headings**, or **URL structure**.
- When adding or updating **structured data (JSON-LD)** for articles, products, organizations, FAQs, or breadcrumbs.
- When configuring or reviewing **robots.txt**, **sitemaps**, or **canonical URLs**.

## Rules / guidelines

### 1. Technical SEO

#### Crawlability and robots

- Ensure there is a sensible `robots.txt` that:
  - Allows crawling of public pages.
  - Blocks obviously private/admin areas (e.g. `/admin/`, `/api/`, `/private/`).
  - Does **not** block resources needed for rendering (CSS, JS, images).
  - References the sitemap URL when available.

```text
User-agent: *
Allow: /

Disallow: /admin/
Disallow: /api/
Disallow: /private/

Sitemap: https://example.com/sitemap.xml
```

- Use `<meta name="robots" />` to control indexability on a **per-page** basis:
  - Default indexable pages: `<meta name="robots" content="index, follow" />`.
  - Non-indexable: `<meta name="robots" content="noindex, nofollow" />`.
  - Indexable but no-follow links: `<meta name="robots" content="index, nofollow" />`.

#### Canonical URLs

- For canonical pages, add a `<link rel="canonical" />` pointing to the preferred URL.
- Use self-referencing canonicals on most pages to prevent duplicate content issues.
- For paginated or filtered content, point the canonical to the primary, canonical version when appropriate.

```html
<link rel="canonical" href="https://example.com/current-page" />
```

#### XML sitemap

- Generate and maintain an XML sitemap that:
  - Includes only **canonical, indexable** URLs.
  - Updates `lastmod` when content changes.
  - Uses a sitemap index if needed for large sites.
- Submit the sitemap to Google Search Console when possible.

### 2. URL structure

- Prefer clean, human-readable, keyword-rich URLs:

```text
✅ https://example.com/products/blue-widget
✅ https://example.com/blog/how-to-use-widgets

❌ https://example.com/p?id=12345
❌ https://example.com/products/item/category/subcategory/blue-widget-2024-sale-discount
```

- URL guidelines:
  - Use **hyphens**, not underscores.
  - Use **lowercase** only.
  - Keep URLs reasonably short (prefer < 75 characters).
  - Include target keywords naturally, without stuffing.
  - Use HTTPS for all pages and assets.

### 3. On-page SEO

#### Title tags

- Every indexable page must have a unique, descriptive `<title>`.
- Include the primary keyword near the beginning.
- Keep titles around **50–60 characters**.
- Optionally append the brand name at the end.

```html
<title>Blue Widgets for Sale | Premium Quality | Example Store</title>
```

#### Meta descriptions

- Add a `<meta name="description" />` for indexable pages.
- Keep descriptions around **150–160 characters**.
- Make them compelling, with a clear value proposition and (when appropriate) a call to action.
- Include the primary keyword naturally and avoid keyword stuffing.

```html
<meta
  name="description"
  content="Shop premium blue widgets with free shipping and 30-day returns. Rated 4.9/5 by thousands of customers."
/>
```

#### Heading structure

- Use a **single `<h1>`** per page that reflects the main topic.
- Follow a logical heading hierarchy: `h1 → h2 → h3 → …` without skipping levels.
- Use descriptive headings that include relevant keywords naturally.

```html
<h1>Blue Widgets - Premium Quality</h1>
<h2>Product Features</h2>
<h3>Durability</h3>
<h3>Design</h3>
<h2>Customer Reviews</h2>
```

#### Image SEO

- Use descriptive filenames (e.g. `blue-widget-product-photo.webp`).
- Provide meaningful `alt` text that describes the image content and context.
- Use modern image formats (WebP/AVIF) where possible, with appropriate sizing and compression.
- Use `loading="lazy"` for below-the-fold images.

```html
<img
  src="blue-widget-product-photo.webp"
  alt="Blue widget with chrome finish, side view showing control panel"
  width="800"
  height="600"
  loading="lazy"
/>
```

#### Internal linking

- Use descriptive anchor text instead of "click here" or "read more".
- Link to relevant internal pages that help users and search engines understand site structure.
- Fix or remove broken links when identified.

```html
<a href="/products/blue-widgets">Browse our blue widget collection</a>
```

### 4. Structured data (JSON-LD)

- Prefer **JSON-LD** for structured data.
- At minimum, consider:
  - `Organization` schema for the site/company.
  - `Article` or `BlogPosting` for content pages.
  - `Product` for product pages.
  - `FAQPage` for FAQ sections.
  - `BreadcrumbList` for breadcrumb navigation.
- Ensure structured data matches the visible content and URLs.
- Validate structured data with Google Rich Results Test or Schema.org Validator.

### 5. Mobile SEO basics

- Use a responsive viewport meta tag:

```html
<meta name="viewport" content="width=device-width, initial-scale=1" />
```

- Ensure tap targets are large enough (∼48x48px).
- Use readable font sizes on mobile (e.g. `16px` base with adequate line-height).

## Checklist

- [ ] Important pages are served over **HTTPS** and resources use secure URLs.
- [ ] `robots.txt` allows crawling of public content and does not block critical assets.
- [ ] Important pages are **indexable** (no unintended `noindex`).
- [ ] Canonical URLs are set correctly and avoid duplicate content issues.
- [ ] XML sitemap exists, contains canonical URLs, and is kept up to date.
- [ ] Each indexable page has a unique, descriptive `<title>`.
- [ ] Each indexable page has a compelling, unique meta description.
- [ ] Heading structure is logical with a single `<h1>` per page.
- [ ] Images have descriptive filenames and `alt` text where appropriate.
- [ ] Internal links use descriptive anchor text and avoid obvious broken links.
- [ ] Appropriate structured data (JSON-LD) is implemented and validates without critical errors.
- [ ] Site is mobile-friendly: responsive viewport, readable fonts, and adequate tap targets.

## Examples (optional)

- Adapt the code examples in this file (robots.txt, meta tags, JSON-LD snippets) to the specific framework or stack you are working with (e.g. Next.js, Remix, plain HTML).

