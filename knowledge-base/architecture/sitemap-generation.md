# Sitemap Generation Guide

Every multi-page site needs a sitemap.xml to help search engines discover all URLs. This guide covers auto-generation for Eleventy (11ty) sites.

## Why Sitemaps Matter

- **SEO** — search engines can find every page, even deep ones not linked from homepage
- **GEO** — AI search engines (Perplexity, ChatGPT Search, Google AI Overviews) use sitemaps to understand site structure
- **Discovery** — new pages get indexed faster when listed in sitemap.xml

## Quick Generate

Add this to your Eleventy config (`.eleventy.js`):

```js
eleventyConfig.addCollection("allPages", function(collectionApi) {
  return collectionApi.getFilteredByGlob("src/*.md").concat(
    collectionApi.getFilteredByGlob("src/*.html")
  );
});

eleventyConfig.addShortcode("sitemap", function() {
  const { collections } = this.ctx;
  const pages = collections.allPages || [];
  const xml = pages.map(page => {
    const url = page.url.replace(/\/$/, '') || '/';
    const lastmod = page.date ? new Date(page.date).toISOString().split('T')[0] : new Date().toISOString().split('T')[0];
    return `<url>
  <loc>https://yoursite.com${url}</loc>
  <lastmod>${lastmod}</lastmod>
  <changefreq>${url === '/' ? 'weekly' : 'monthly'}</changefreq>
  <priority>${url === '/' ? '1.0' : '0.8'}</priority>
</url>`;
  }).join('\n');

  return `<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
${xml}
</urlset>`;
});
```

Then create `src/sitemap.njk`:

```html
---
permalink: /sitemap.xml
eleventyExcludeFromCollections: true
---
{{ sitemap | safe }}
```

## Manual Sitemap (No Collection)

For simple sites, create `src/sitemap.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://yoursite.com/</loc>
    <changefreq>weekly</changefreq>
    <priority>1.0</priority>
  </url>
  <url>
    <loc>https://yoursite.com/about/</loc>
    <changefreq>monthly</changefreq>
    <priority>0.8</priority>
  </url>
  <url>
    <loc>https://yoursite.com/services/</loc>
    <changefreq>monthly</changefreq>
    <priority>0.8</priority>
  </url>
  <url>
    <loc>https://yoursite.com/contact/</loc>
    <changefreq>monthly</changefreq>
    <priority>0.8</priority>
  </url>
</urlset>
```

## Submit to Search Engines

After deploying, submit your sitemap:

- **Google:** https://search.google.com/search-console/sitemaps
- **Bing:** https://www.bing.com/webmasters/sitemaps
- **Others:** Most search engines auto-discover from robots.txt

Add to `src/robots.txt`:

```
User-agent: *
Allow: /
Sitemap: https://yoursite.com/sitemap.xml
```

## Best Practices

1. **One sitemap per site** — don't fragment unless you have 50,000+ URLs
2. **Update frequency** — set `changefreq` realistically (daily/weekly/monthly)
3. **Priority** — home gets 1.0, deep pages get 0.6-0.8
4. **Validate** — use [sitemap.org validator](https://www.sitemaps.org/validator.html)
5. **Keep it fresh** — regenerate on every build (Eleventy does this automatically)

## Agent Pattern

When scaffolding a new Pagify site:

1. Create sitemap template in project
2. Add `permalink: /sitemap.xml` to page frontmatter
3. Update sitemap with actual URLs after content is drafted
4. Include in pre-launch checklist

This ensures every deployed site is immediately discoverable by both traditional search engines and AI search engines.