# Pagify Starter Template

A ready-to-use, multi-page static site built with [Eleventy](https://www.11ty.dev/). Clone it, customize it, deploy it.

## What You Get

- **4 pages** — Home, About, Services, Contact (each its own URL)
- **SEO** — meta tags, canonical URLs, semantic HTML on every page
- **GEO** — JSON-LD structured data for AI search engines
- **Accessible** — `aria-current`, landmark elements, skip-to-content ready
- **Mobile-first CSS** — no framework, no build step, just clean custom properties
- **Deploy anywhere** — Cloudflare Pages, Netlify, Vercel, GitHub Pages, or any static host

## Quick Start

```bash
# Clone and enter
cp -r starter my-site && cd my-site

# Install
npm install

# Dev server (hot reload)
npm run dev

# Production build
npm run build
# Output: _site/
```

## Customizing

1. **Edit `src/_data/site.json`** — site name, tagline, author, navigation links
2. **Edit page files** (`src/index.md`, `src/about.md`, etc.) — your content in Markdown
3. **Edit `src/css/style.css`** — colors, fonts, spacing via CSS custom properties
4. **Add images** to `src/images/` — they're copied to `_site/` automatically

## File Structure

```
starter/
├── .eleventy.js          # Eleventy config
├── package.json
├── src/
│   ├── _data/site.json   # Global site data
│   ├── _layouts/base.njk # Page template
│   ├── css/style.css     # Styles
│   ├── images/           # Static images
│   ├── fonts/            # Custom fonts
│   ├── index.md          # Home page
│   ├── about.md          # About page
│   ├── services.md       # Services page
│   └── contact.md        # Contact page
└── _site/                # Built output (gitignored)
```

## Deploying

The `_site/` folder is your entire website. Upload it anywhere.

**Cloudflare Pages:** Connect repo → build command `npm run build` → output dir `starter/_site`

**Manual:** Run `npm run build` and upload `_site/` to any static host.

See the [Pagify knowledge base](../knowledge-base/) for detailed hosting guides.

## Philosophy

- Real multi-page HTML, not a single-page app
- Every page is its own URL, crawlable by search engines and AI engines
- No JavaScript required at runtime
- You own the output — eject anytime
