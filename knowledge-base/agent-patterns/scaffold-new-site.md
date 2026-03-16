# Agent Pattern: Scaffold a New Pagify Site

_Last verified: 2026-03-16_

## Summary

Step-by-step playbook for an AI agent (Claw) to take a client from raw intake to a deployable Eleventy site. This is the foundational pattern — every Pagify site starts here.

## Prerequisites

- Node.js 18+ installed on the build machine
- Git configured with push access to a GitHub repo
- Client intake completed (see [Intake Checklist](#intake-checklist) below)

## Intake Checklist

Before writing a single file, the agent (or coach) must gather:

| Field | Required | Example |
|---|---|---|
| **Site name** | Yes | "Jane Smith Financial Coaching" |
| **Primary purpose** | Yes | "Attract local families to free workshops" |
| **Target audience** | Yes | "Families in Raleigh, NC with kids under 18" |
| **Services offered** | Yes | List of 3-5 specific services |
| **Location** | Yes | City, State (for local SEO/GEO) |
| **Contact method** | Yes | Phone, email, or booking link |
| **Brand colors** | No | Hex codes or "use defaults" |
| **Existing content** | No | Bio text, testimonials, photos |
| **Domain name** | No | Can add later |

**Rule:** Don't start building until purpose and audience are clear. A beautiful site with no focus is worse than no site.

## Step 1: Initialize the Project

```bash
mkdir my-site && cd my-site
npm init -y
npm install --save-dev @11ty/eleventy
```

Create `.eleventy.js`:

```js
module.exports = function(eleventyConfig) {
  eleventyConfig.addPassthroughCopy("src/css");
  eleventyConfig.addPassthroughCopy("src/images");

  return {
    dir: {
      input: "src",
      output: "_site",
      includes: "_includes",
      data: "_data"
    },
    markdownTemplateEngine: "njk"
  };
};
```

Create `.gitignore`:

```
node_modules/
_site/
.DS_Store
```

Update `package.json` scripts:

```json
{
  "scripts": {
    "build": "npx @11ty/eleventy",
    "serve": "npx @11ty/eleventy --serve"
  }
}
```

## Step 2: Create the Data Layer

### `src/_data/site.json`

```json
{
  "title": "{{SITE_NAME}}",
  "url": "https://{{DOMAIN_OR_PLACEHOLDER}}.pages.dev",
  "description": "{{ONE_LINE_DESCRIPTION}}",
  "language": "en",
  "geo": {
    "locality": "{{CITY}}",
    "region": "{{STATE}}",
    "country": "US",
    "serviceTypes": ["{{SERVICE_1}}", "{{SERVICE_2}}"]
  }
}
```

### `src/_data/navigation.json`

```json
[
  { "label": "Home", "url": "/" },
  { "label": "About", "url": "/about/" },
  { "label": "Services", "url": "/services/" },
  { "label": "FAQ", "url": "/faq/" },
  { "label": "Contact", "url": "/contact/" }
]
```

**Agent note:** Always generate at least 5 pages. A single-page site defeats Pagify's purpose. Each page = its own URL = its own SEO/GEO surface.

## Step 3: Create the Base Layout

### `src/_includes/base.njk`

```html
<!DOCTYPE html>
<html lang="{{ site.language }}">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>{{ title }} — {{ site.title }}</title>
  <meta name="description" content="{{ description or site.description }}">
  <link rel="canonical" href="{{ site.url }}{{ page.url }}">

  <!-- Open Graph -->
  <meta property="og:title" content="{{ title }}">
  <meta property="og:description" content="{{ description or site.description }}">
  <meta property="og:type" content="website">
  <meta property="og:url" content="{{ site.url }}{{ page.url }}">
  <meta property="og:image" content="{{ image or site.url + '/images/og-default.jpg' }}">
  <meta property="og:site_name" content="{{ site.title }}">

  <!-- Twitter Card -->
  <meta name="twitter:card" content="summary_large_image">
  <meta name="twitter:title" content="{{ title }}">
  <meta name="twitter:description" content="{{ description or site.description }}">
  <meta name="twitter:image" content="{{ image or site.url + '/images/og-default.jpg' }}">

  <link rel="stylesheet" href="/css/style.css">

  <!-- Site-wide structured data -->
  <script type="application/ld+json">
  {
    "@context": "https://schema.org",
    "@type": "WebSite",
    "name": "{{ site.title }}",
    "url": "{{ site.url }}",
    "description": "{{ site.description }}"
  }
  </script>
  {% block head %}{% endblock %}
</head>
<body>
  <header>
    <nav aria-label="Main navigation">
      <a href="/" class="site-title">{{ site.title }}</a>
      <ul>
        {%- for item in navigation %}
        <li><a href="{{ item.url }}"{% if page.url == item.url %} aria-current="page"{% endif %}>{{ item.label }}</a></li>
        {%- endfor %}
      </ul>
    </nav>
  </header>

  <main>
    {{ content | safe }}
  </main>

  <footer>
    <p>© {{ site.title }} · {{ site.geo.locality }}, {{ site.geo.region }}</p>
  </footer>
</body>
</html>
```

### `src/_includes/page.njk`

```html
---
layout: base.njk
---
<article>
  <h1>{{ title }}</h1>
  {{ content | safe }}
</article>
```

## Step 4: Generate Content Pages

The agent writes each page as Markdown with front matter. Content must follow these rules:

1. **Unique title and description per page** (see [SEO Meta Tags Checklist](/knowledge-base/seo/meta-tags-checklist.md))
2. **Extractable facts, not marketing fluff** (see [GEO Guide](/knowledge-base/geo/generative-engine-optimization.md))
3. **Real information from intake** — never generate placeholder text like "Lorem ipsum" or "Your text here"
4. **FAQ page with real questions** — at least 5 questions a real client would ask

### Minimum pages for any site:

| Page | File | Purpose |
|---|---|---|
| Home | `src/index.md` | Who you are, what you do, where — in 3 seconds |
| About | `src/about.md` | Specific bio, credentials, philosophy |
| Services | `src/services.md` | Each service listed with clear description |
| FAQ | `src/faq.md` | Real questions, direct answers (GEO gold) |
| Contact | `src/contact.md` | Address, phone, email, hours — structured data |

### Example: `src/index.md`

```markdown
---
title: Home
description: Financial literacy workshops and coaching for families in Raleigh, NC
layout: page.njk
---

## Helping Raleigh Families Build Financial Confidence

Jane Smith Financial Coaching offers **free monthly workshops** and
**one-on-one coaching** for families in the Triangle area.

### What We Offer

- **Financial literacy workshops** — monthly, free, open to the public
- **One-on-one coaching** — retirement planning, debt reduction, college savings
- **Community events** — quarterly meetups connecting families with shared goals

### Who This Is For

Families in Raleigh, Durham, and Chapel Hill who want to take control of
their finances — no prior knowledge needed.

[Get started →](/contact/)
```

## Step 5: Add Structured Data for Key Pages

The agent must add JSON-LD to the homepage and contact page at minimum. Use the `{% block head %}` in `base.njk`:

### Homepage: add `ProfessionalService` schema
### Contact page: add `LocalBusiness` schema with address
### FAQ page: add `FAQPage` schema with all Q&A pairs

See the [GEO Guide](/knowledge-base/geo/generative-engine-optimization.md) for exact markup.

## Step 6: Create Minimal CSS

### `src/css/style.css`

```css
/* Pagify default — readable, accessible, fast */
:root {
  --text: #1a1a1a;
  --bg: #ffffff;
  --accent: #2563eb;
  --gray: #6b7280;
  --max-width: 42rem;
}

* { margin: 0; padding: 0; box-sizing: border-box; }

body {
  font-family: system-ui, -apple-system, sans-serif;
  color: var(--text);
  background: var(--bg);
  line-height: 1.6;
}

header, main, footer {
  max-width: var(--max-width);
  margin: 0 auto;
  padding: 1rem;
}

nav ul { list-style: none; display: flex; gap: 1rem; }
nav a { color: var(--text); text-decoration: none; }
nav a[aria-current="page"] { font-weight: bold; }

h1, h2, h3 { margin-top: 1.5rem; margin-bottom: 0.5rem; }
p, ul, ol { margin-bottom: 1rem; }
a { color: var(--accent); }

footer { margin-top: 3rem; color: var(--gray); font-size: 0.875rem; }
```

**Agent note:** Keep CSS minimal. No frameworks. No build tools for CSS. The stylesheet should be readable by a coach who knows basic CSS. If the client provides brand colors, update the CSS custom properties.

## Step 7: Build and Verify

```bash
npm run build
npm run serve  # Preview at localhost:8080
```

### Verification checklist (agent must confirm all):

- [ ] Every page has a unique `<title>` and `<meta name="description">`
- [ ] All internal links work (no 404s)
- [ ] Navigation highlights the current page
- [ ] Structured data validates (paste into [Google Rich Results Test](https://search.google.com/test/rich-results))
- [ ] Site renders correctly without JavaScript
- [ ] Mobile responsive (check at 375px width)
- [ ] All placeholder text has been replaced with real content

## Step 8: Deploy

```bash
git init
git add .
git commit -m "Initial site: {{SITE_NAME}}"
git remote add origin https://github.com/{{OWNER}}/{{REPO}}.git
git push -u origin main
```

Then connect to Cloudflare Pages (see [Cloudflare Pages evaluation](/knowledge-base/hosting/cloudflare-pages.md)).

## Common Agent Mistakes

1. **Generating a single-page site** — Pagify is explicitly multi-page. Minimum 5 pages.
2. **Using placeholder content** — "Lorem ipsum" or "[Your text here]" is never acceptable. If intake info is missing, ask — don't fake it.
3. **Skipping structured data** — JSON-LD is not optional. It's what makes GEO work.
4. **Over-engineering CSS** — No Tailwind, no CSS-in-JS, no build step. Plain CSS that a human can read.
5. **Forgetting `og:image`** — Social shares without an image get ignored. Create or request a default image.
6. **Not testing the build** — Always run `npm run build` and verify output before pushing.

## For Coaches Reviewing Agent Output

When an agent says "the site is ready," check:

1. Run `npm run serve` — does it actually build?
2. Open each page — is the content real (not placeholder)?
3. View source on the homepage — do you see JSON-LD structured data?
4. Resize the browser to mobile width — does it still work?
5. Check that every page in the navigation actually exists

If any of these fail, send the agent back. The site isn't ready.

## References

- [Eleventy Project Structure](/knowledge-base/architecture/eleventy-project-structure.md)
- [SEO Meta Tags Checklist](/knowledge-base/seo/meta-tags-checklist.md)
- [GEO Foundational Guide](/knowledge-base/geo/generative-engine-optimization.md)
- [Cloudflare Pages Deployment](/knowledge-base/hosting/cloudflare-pages.md)
