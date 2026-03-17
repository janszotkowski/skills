---
name: frontend-a11y
description: Apply accessibility patterns aligned with WCAG 2.2 AA and EN 301 549 (EU accessibility standard) to every frontend page or flow. Use when adding or editing pages, forms, async actions, or interactive components.
tags: [frontend, a11y, web]
scope: agent-guidance
version: 0.1.0
status: stable
---

## Goal

Meet **WCAG 2.2 AA** and **EN 301 549** requirements wherever reasonably possible in this app. Every new or updated frontend page or flow should follow the rules below.

## When to use

- When adding or editing any **frontend page**, **view**, or **layout**.
- When implementing or refactoring **forms**, **inputs**, or **validation flows**.
- When adding or updating **async actions** and **status messages** (loading, success, error).
- When introducing or changing **icons**, **images**, or other decorative UI elements.

## Rules / guidelines

### 1. Skip link and main content

- First focusable element on the page: a skip link with text.
- `href="#main-content"`; `<main id="main-content" tabIndex={-1}>`.
- Skip link is visually hidden by default and becomes visible on focus, e.g. using Tailwind:

```tsx
className="sr-only focus:not-sr-only focus:absolute focus:left-4 focus:top-4 ..."
```

This pattern helps keyboard and screen reader users quickly jump to main content, which is expected by EU accessibility guidance.

---

### 2. Landmarks

- Use semantic landmarks: `<header>`, `<main id="main-content">`, `<footer role="contentinfo">` according to the layout of the page.
- For sections, provide a heading with a unique `id`, and set `aria-labelledby="that-heading-id"` on the section container.

This gives assistive technologies a clear, structured outline of the page, which is critical for WCAG’s navigability requirements.

---

### 3. Forms and inputs

- The `<form>` element should have an accessible name:
  - Prefer a visible heading near the form, or
  - Use `aria-label` with a description from translations when no visible label is suitable.
- Every input must have either:
  - A properly associated `<label>` (visible or visually hidden), or
  - A clear `aria-label` from translations.
- Add `autoComplete` where appropriate (email, name, new-password, organization, etc.) to support browser and assistive technology hints.
- Submit / primary action buttons must be `disabled` while loading (to prevent double submits and confusion).

These rules address multiple WCAG and EN 301 549 success criteria around form labels, instructions, and error prevention.

---

### 4. Live regions (changing status)

- The container where async status messages appear (success, loading, error) should have:
  - `role="status"`,
  - `aria-live="polite"`,
  - `aria-atomic="true"`.
- While an async operation is in progress, set `aria-busy={true}` on this container.

This ensures that screen readers announce loading states and results without being overly disruptive, matching EU accessibility expectations for dynamic content.

---

### 5. Decorative elements

- Icons and images that are purely decorative (do not convey information not already in text) must have `aria-hidden="true"`.
- If an icon or image is meaningful, provide an accessible name via:
  - Nearby text, or
  - `aria-label`, or
  - `alt` text on `<img>`.

---

### 6. Lists

- Where a group of items is logically a list, use semantic lists:
  - Prefer `<ul>` / `<ol>` with `<li>` items.
  - If using generic containers, ensure:
    - The wrapper has `role="list"`,
    - Each item has `role="listitem"`.

This helps assistive technologies announce item counts and relationships.

---

### 7. Headings

- Maintain a proper heading hierarchy: `h1` → `h2` → `h3` → `h4` without skipping levels.
- Each major section should have a clear heading; avoid styling generic elements to “look like” headings without using actual `h*` tags.

Correct heading structure is an explicit requirement for perceivable and navigable content under WCAG / EN 301 549.

---

### 8. Project formatting convention

- For JSX/TSX elements with multiple props, put **each prop on its own line**. This is enforced by ESLint and also keeps accessibility-related attributes (like `aria-*`) easy to spot and review.

## Checklist

- [ ] Page has a working skip link that jumps to `<main id="main-content">`.
- [ ] Landmarks use semantic elements (`header`, `main`, `footer`) and sections use `aria-labelledby` where appropriate.
- [ ] All forms and inputs have accessible labels (`label` or `aria-label`), and primary buttons are disabled during loading.
- [ ] Async status area uses `role="status"`, `aria-live="polite"`, and `aria-busy` while loading.
- [ ] Decorative icons/images are marked `aria-hidden="true"`, and meaningful graphics have accessible names.
- [ ] Headings follow a logical `h1 → h2 → …` order, without skipping levels.
- [ ] Implementation choices are consistent with WCAG 2.2 AA and EN 301 549 where applicable.
