# Customizing the Pagify Starter Template

The Pagify starter gives you a production-ready multi-page site out of the box. Here's how to make it yours.

## Quick Customization

### 1. Site Metadata

Edit `src/_data/site.json`:

```json
{
  "name": "Your Site Name",
  "tagline": "What you do in one line",
  "url": "https://yourdomain.com",
  "author": "Your Name",
  "description": "What the site is about (for search engines)",
  "language": "en",
  "nav": [
    { "text": "Home", "url": "/" },
    { "text": "About", "url": "/about/" },
    { "text": "Services", "url": "/services/" },
    { "text": "Contact", "url": "/contact/" }
  ]
}
```

### 2. Navigation

Add or remove items in the `nav` array. Each item needs:
- `text` — what shows in the menu
- `url` — the page path (must exist in `src/`)

### 3. Colors and Fonts

Edit `src/css/style.css`. The design uses CSS custom properties:

```css
:root {
  --color-text: #1a1a1a;        /* Main text */
  --color-bg: #ffffff;          /* Background */
  --color-accent: #2563eb;      /* Links, buttons */
  --color-accent-hover: #1d4ed8;
  --color-muted: #6b7280;       /* Secondary text */
  --color-border: #e5e7eb;      /* Borders */
  --font-body: system-ui, -apple-system, sans-serif;
  --max-width: 48rem;           /* Max content width */
  --space: 1.5rem;              /* Base spacing */
}
```

Change the hex values to match your brand.

## Adding New Pages

### 1. Create the Markdown file

In `src/`, create `newpage.md`:

```yaml
---
layout: base.njk
title: New Page Name
description: Brief description for SEO.
---

# Your Page Heading

Your content here...
```

### 2. Add to navigation

In `site.json`, add to the `nav` array:

```json
{ "text": "New Page", "url": "/newpage/" }
```

The URL auto-matches the filename.

## Adding Images

### 1. Add the image

Drop files into `src/images/`. Supported formats: PNG, JPG, WebP, SVG.

### 2. Reference in Markdown

```markdown
![Alt text](/images/filename.jpg)
```

Images are copied to `_site/images/` at build time.

## Adding Custom Fonts

### 1. Add font files

Put files in `src/fonts/`.

### 2. Update CSS

```css
@font-face {
  font-family: 'MyFont';
  src: url('/fonts/myfont.woff2') format('woff2');
}

:root {
  --font-body: 'MyFont', system-ui, sans-serif;
}
```

## Changing the Layout

Edit `src/_layouts/base.njk` — this is the master template for all pages.

Common customizations:

### Add a hero image on the home page
```html
{% if page.url == "/" %}
<img src="/images/hero.jpg" alt="Hero" class="hero-image">
{% endif %}
```

### Add a contact form
See [forms-and-contact.md](../architecture/forms-and-contact.md) for Formspree, Netlify Forms, or email-only options.

### Add a blog section
Create `src/blog/` directory and add markdown files. Add a blog index page:

```yaml
---
layout: base.njk
title: Blog
---

# Blog

<ul>
{% for post in collections.blog %}
  <li><a href="{{ post.url }}">{{ post.data.title }}</a></li>
{% endfor %}
</ul>
```

## Building for Production

```bash
npm run build
```

Output goes to `_site/` — upload this folder to any static host.

## Next Steps

- [Pre-launch checklist](../launch/pre-launch-checklist.md)
- [Deploy to Cloudflare Pages](../hosting/cloudflare-pages.md)
- [SEO checklist](../seo/seo-checklist.md)