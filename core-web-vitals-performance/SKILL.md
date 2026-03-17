---
name: core-web-vitals-performance
description: Optimize web apps for Core Web Vitals (LCP, INP, CLS, FID/TTI, TTFB) using modern frontend performance best practices. Use when building or refactoring UI that affects loading, interactivity, or visual stability.
tags: [performance, core-web-vitals, frontend, web]
scope: agent-guidance
version: 0.1.0
status: draft
---

## Goal

Help the agent consistently ship frontend code that meets or moves towards **good Core Web Vitals scores** (LCP, INP, CLS, FID/TTI, TTFB) by applying practical, framework-agnostic performance patterns.

## When to use

- When building or refactoring:
  - Layouts, navigation, or shells that are present on most pages.
  - Above-the-fold content, hero sections, or large media.
  - Interactive components that can block input (modals, carousels, heavy forms, dashboards).
- When diagnosing or preventing:
  - Slow initial load / page transitions.
  - Input lag, jank, or long tasks on the main thread.
  - Layout shifts on load or during interactions.
- When reviewing performance-related PRs or running Lighthouse / WebPageTest audits.

## Rules / guidelines

### 1. Largest Contentful Paint (LCP)

**Goal**: LCP < 2.5s on good connections for key pages.

- Identify the LCP element (often hero image, heading, or main content block) and:
  - Load it as early as reasonably possible.
  - Use proper `width`/`height` or aspect-ratio to avoid layout shifts.
- For hero images:
  - Use responsive images (`srcset`, `sizes`) and modern formats (WebP/AVIF) with reasonable compression.
  - Avoid blocking the image on non-critical JS; let the browser do its job.
  - Consider `priority` / `fetchpriority="high"` (or framework equivalent) for the main hero image.
- Minimize render‑blocking resources:
  - Inline only minimal critical CSS or use well-optimized CSS loading.
  - Defer non-essential scripts (`defer`, `async`, or code-splitting).

### 2. Interaction to Next Paint (INP) / First Input Delay (FID)

**Goal**: INP < 200ms for typical user interactions.

- Avoid long tasks on the main thread:
  - Split heavy synchronous work into smaller chunks (e.g. `setTimeout`, `requestIdleCallback`) or move to Web Workers where appropriate.
  - Avoid large, synchronous JSON parsing or complex loops on initial render.
- For expensive UI updates:
  - Use memoization (e.g. `React.memo`, `useMemo`, `useCallback`) when it significantly reduces re-renders.
  - Prefer virtualization for large lists/tables instead of rendering hundreds/thousands of rows at once.
- Defer non-critical work until after first meaningful interaction:
  - Analytics, feature flags, or low-priority background fetches should not block primary UI.

### 3. Cumulative Layout Shift (CLS)

**Goal**: CLS < 0.1.

- Always reserve space for images and media:
  - Provide explicit `width` and `height` or use CSS `aspect-ratio`.
  - Avoid loading images with unknown dimensions above the fold.
- Avoid UI that shifts layout unexpectedly:
  - Do not insert banners/toasts above existing content; overlay them or reserve space ahead of time.
  - For lazy-loaded components, render placeholders with the same approximate size.
- Be careful with custom fonts:
  - Use appropriate `font-display` (e.g. `swap`, `optional`).
  - Consider preloading critical fonts, but test impact on LCP.

### 4. Bundle size and code-splitting

- Keep JavaScript bundles as small as reasonably possible:
  - Prefer code-splitting for heavy routes (dashboards, admin areas) and rarely-used components.
  - Avoid importing large libraries globally when only a few routes need them.
- Watch for heavy dependencies:
  - Prefer smaller, focused libraries over kitchen-sink bundles.
  - Remove unused polyfills or libraries when dropping old browser support.
- Use dynamic imports / lazy loading for:
  - Charts, rich text editors, maps, or other heavy widgets.
  - Non-critical panels or modals that are not visible on initial load.

### 5. Images, media, and assets

- Use the right format and size:
  - Prefer WebP/AVIF with fallbacks for modern browsers.
  - Avoid serving multi‑megabyte images; resize and compress appropriately.
- Use `loading="lazy"` for below‑the‑fold images; avoid lazy loading the main hero image.
- Use CDNs or edge caching when possible for static assets.

### 6. Data fetching and caching

- Avoid blocking the main thread with heavy data transformations on initial render.
- Cache data at appropriate layers:
  - Browser-side (query libraries, SWR-style caches).
  - HTTP caching (ETag/Last-Modified, Cache-Control) when applicable.
- For repeat navigations, prefer:
  - Client-side routing with prefetching where user intent is clear.
  - Keeping critical data in memory instead of refetching it unnecessarily.

### 7. Measurement and verification

- Use tools like Lighthouse, Chrome DevTools, WebPageTest, or framework-specific analytics to:
  - Check Core Web Vitals for key pages.
  - Identify slow resources (waterfall view, coverage, performance trace).
- When optimizing, change **one thing at a time** and re‑measure:
  - Capture before/after metrics.
  - Focus on the slowest, most important user journeys (e.g. landing → login → main dashboard).

## Checklist

- [ ] LCP element (hero heading/image/main content) loads quickly and has reserved space (no jank).
- [ ] No obvious layout shifts during load or interactions; CLS is within acceptable limits.
- [ ] There are no long tasks blocking user input; interactions feel responsive.
- [ ] Large or rarely-used components are lazy-loaded or code-split.
- [ ] Images are optimized (format, dimensions, compression) and lazy-loaded where appropriate.
- [ ] Heavy data processing is minimized on initial render, with caching where it makes sense.
- [ ] Key flows have been checked with Lighthouse or similar tooling and show improving or good Core Web Vitals.

## Examples (optional)

- Add project-specific examples later:
  - Before/after diffs for a hero section optimization.
  - Replacing a heavy table with virtualized rendering.
  - Refactoring a long-running effect into batched or deferred work.

