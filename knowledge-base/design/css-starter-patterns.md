# CSS Starter Patterns — Minimal, Responsive, No-Framework

_Last verified: 2026-03-29_

## Summary

A battle-tested CSS foundation for Pagify sites. No frameworks, no build tools, no classes to memorize. Just clean defaults that make any Markdown content look professional on any device.

Coaches: drop this into `src/css/style.css` as the starting point for every new site. Customize colors and fonts, but the structure stays.

## The Complete Starter

```css
/* ============================
   Pagify CSS Starter v1.0
   Drop-in foundation for Eleventy sites
   ============================ */

/* --- Reset & Box Model --- */
*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

/* --- Custom Properties (edit these) --- */
:root {
  --color-text: #1a1a1a;
  --color-bg: #ffffff;
  --color-accent: #2563eb;
  --color-accent-hover: #1d4ed8;
  --color-muted: #6b7280;
  --color-border: #e5e7eb;
  --color-surface: #f9fafb;

  --font-body: system-ui, -apple-system, "Segoe UI", sans-serif;
  --font-heading: var(--font-body);
  --font-mono: ui-monospace, "Cascadia Code", "Fira Code", monospace;

  --max-width: 42rem;
  --spacing: 1.5rem;
}

/* --- Base Typography --- */
html {
  font-size: 100%; /* 16px default — never override this */
  scroll-behavior: smooth;
}

body {
  font-family: var(--font-body);
  color: var(--color-text);
  background: var(--color-bg);
  line-height: 1.7;
  -webkit-font-smoothing: antialiased;
}

h1, h2, h3, h4 {
  font-family: var(--font-heading);
  line-height: 1.3;
  margin-top: 2em;
  margin-bottom: 0.5em;
}

h1 { font-size: 2rem; }
h2 { font-size: 1.5rem; }
h3 { font-size: 1.25rem; }
h4 { font-size: 1.1rem; }

p, ul, ol, blockquote, pre {
  margin-bottom: var(--spacing);
}

a {
  color: var(--color-accent);
  text-decoration: underline;
  text-underline-offset: 0.15em;
}

a:hover {
  color: var(--color-accent-hover);
}

/* --- Layout: Centered Content Column --- */
main {
  max-width: var(--max-width);
  margin: 0 auto;
  padding: var(--spacing);
}

/* --- Navigation --- */
nav {
  max-width: var(--max-width);
  margin: 0 auto;
  padding: var(--spacing);
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  gap: 1rem;
  border-bottom: 1px solid var(--color-border);
}

nav a {
  text-decoration: none;
  color: var(--color-text);
  font-weight: 500;
}

nav a:hover {
  color: var(--color-accent);
}

nav a:first-child {
  font-weight: 700;
  margin-right: auto;
}

/* --- Footer --- */
footer {
  max-width: var(--max-width);
  margin: 0 auto;
  padding: calc(var(--spacing) * 2) var(--spacing);
  border-top: 1px solid var(--color-border);
  color: var(--color-muted);
  font-size: 0.875rem;
}

/* --- Images --- */
img {
  max-width: 100%;
  height: auto;
  display: block;
  border-radius: 0.25rem;
}

/* --- Blockquotes --- */
blockquote {
  border-left: 3px solid var(--color-accent);
  padding-left: 1rem;
  color: var(--color-muted);
  font-style: italic;
}

/* --- Code --- */
code {
  font-family: var(--font-mono);
  font-size: 0.9em;
  background: var(--color-surface);
  padding: 0.15em 0.35em;
  border-radius: 0.2rem;
}

pre {
  background: var(--color-surface);
  padding: var(--spacing);
  border-radius: 0.5rem;
  overflow-x: auto;
}

pre code {
  background: none;
  padding: 0;
}

/* --- Tables --- */
table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: var(--spacing);
}

th, td {
  text-align: left;
  padding: 0.5rem 0.75rem;
  border-bottom: 1px solid var(--color-border);
}

th {
  font-weight: 600;
  border-bottom-width: 2px;
}

/* --- Lists --- */
ul, ol {
  padding-left: 1.5em;
}

li + li {
  margin-top: 0.25em;
}

/* --- Utility: Skip Link (accessibility) --- */
.skip-link {
  position: absolute;
  top: -100%;
  left: 0;
  padding: 0.5rem 1rem;
  background: var(--color-accent);
  color: white;
  z-index: 100;
}

.skip-link:focus {
  top: 0;
}

/* --- Responsive: Small Screens --- */
@media (max-width: 640px) {
  h1 { font-size: 1.6rem; }
  h2 { font-size: 1.3rem; }

  nav {
    flex-direction: column;
    align-items: flex-start;
  }

  nav a:first-child {
    margin-right: 0;
  }
}

/* --- Print --- */
@media print {
  nav, footer { display: none; }
  body { font-size: 12pt; line-height: 1.5; }
  a { color: inherit; text-decoration: underline; }
  main { max-width: none; padding: 0; }
}
```

## What This Gives You

| Feature | How |
|---|---|
| **Responsive out of the box** | `max-width` + auto margins + media queries; no breakpoint gymnastics |
| **Readable typography** | System fonts, 1.7 line-height, proper heading hierarchy |
| **Accessible** | Skip link pattern included, good contrast ratios, print styles |
| **Customizable in one place** | Change the CSS custom properties in `:root` — colors, fonts, spacing all update globally |
| **Zero dependencies** | No Tailwind, no Bootstrap, no PostCSS. Just a `.css` file |
| **Works with Markdown output** | Styled through element selectors, not classes — Markdown doesn't generate classes |

## Key Design Decision: Element Selectors, Not Classes

Pagify sites are generated from Markdown. Markdown produces `<h2>`, `<p>`, `<blockquote>`, `<ul>` — it does **not** produce `<div class="card">` or `<section class="hero">`.

This CSS styles elements directly. No class names to remember. Whatever Markdown generates, it looks right.

When coaches need component patterns (hero sections, cards, CTAs), those go in the Nunjucks layouts using minimal, semantic class names — not in the content files.

## Customization Guide for Coaches

### Changing the color scheme

Edit only the `:root` block. Example — warm palette:

```css
:root {
  --color-text: #292524;
  --color-bg: #faf9f7;
  --color-accent: #b45309;
  --color-accent-hover: #92400e;
  --color-muted: #78716c;
  --color-border: #e7e5e4;
  --color-surface: #f5f5f4;
}
```

### Adding a Google Font

```css
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');

:root {
  --font-body: 'Inter', system-ui, sans-serif;
}
```

Put the `@import` at the very top of the file, before everything else.

### CJK (Chinese/Japanese/Korean) font stack

```css
:root {
  --font-body: system-ui, -apple-system, "Segoe UI", "Noto Sans SC", "PingFang SC", "Microsoft YaHei", sans-serif;
}
```

System fonts handle CJK well on modern devices. Only add web fonts if the specific typeface matters.

## Anti-Patterns

- **Don't add Tailwind** — utility classes fight Markdown output; Pagify sites don't need them
- **Don't use `px` for font sizes** — `rem` scales with user preferences (accessibility)
- **Don't remove the print styles** — coaches print handouts for non-technical clients
- **Don't set `max-width` wider than ~45rem** — long lines hurt readability
- **Don't add a CSS reset/normalize library** — the minimal reset above is enough

## References

- [Modern CSS Reset (Andy Bell)](https://piccalil.li/blog/a-more-modern-css-reset/)
- [System Font Stack](https://systemfontstack.com/)
- [CSS Custom Properties (MDN)](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)
- [WCAG Contrast Requirements](https://www.w3.org/WAI/WCAG21/Understanding/contrast-minimum.html)
