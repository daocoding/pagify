# SEO Checklist for Pagify Sites

Every page on a Pagify site should be found by both traditional search engines (Google, Bing) and AI search engines (Perplexity, ChatGPT Search, Google AI Overviews).

## Per-Page Requirements

### 1. Unique Title Tag
```html
<title>{{ title }} — {{ site.name }}</title>
```
- Keep under 60 characters
- Put page-specific content first, site name second

### 2. Unique Meta Description
```html
<meta name="description" content="{{ description | default(site.description) }}">
```
- Keep under 160 characters
- Write for humans, not algorithms — it's what appears in search results

### 3. Canonical URL
```html
<link rel="canonical" href="{{ site.url }}{{ page.url }}">
```
- Prevents duplicate content issues
- Always use absolute URL

### 4. Semantic HTML
Use proper heading hierarchy:
```html
<h1>Page Title</h1>
<h2>Section</h2>
<h3>Subsection</h3>
```
- One `<h1>` per page
- Don't skip levels (h1 → h3 without h2)

### 5. Open Graph (Optional but Recommended)
```html
<meta property="og:title" content="{{ title }}">
<meta property="og:description" content="{{ description }}">
<meta property="og:type" content="website">
<meta property="og:url" content="{{ site.url }}{{ page.url }}">
```

## Structured Data (GEO)

Add JSON-LD on every page:
```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "WebPage",
  "name": "{{ title }}",
  "description": "{{ description }}",
  "url": "{{ site.url }}{{ page.url }}"
}
</script>
```

For organization-level pages, use `Organization` or `Person` schema. See [structured-data-patterns.md](../structured-data/structured-data-patterns.md).

## Technical SEO

### Sitemap
Generate `sitemap.xml` automatically:
```javascript
// .eleventy.js
eleventyConfig.addCollection("sitemap", function(collectionApi) {
  return collectionApi.getFilteredByGlob("src/*.md").map(item => ({
    url: item.url,
    date: item.date
  }));
});
```

### Robots.txt
```text
User-agent: *
Allow: /
Sitemap: https://yoursite.com/sitemap.xml
```

### Performance
- Images optimized (WebP, lazy loading)
- CSS minimal and inlined or single file
- No JS required for content rendering

## Page-Specific Checklist

| Item | Home | About | Services | Blog | Contact |
|------|------|-------|----------|------|---------|
| Unique title | ✅ | ✅ | ✅ | ✅ | ✅ |
| Meta description | ✅ | ✅ | ✅ | ✅ | ✅ |
| Canonical URL | ✅ | ✅ | ✅ | ✅ | ✅ |
| H1 | ✅ | ✅ | ✅ | ✅ | ✅ |
| Open Graph | ✅ | ✅ | ✅ | ✅ | ✅ |
| JSON-LD | ✅ | ✅ | ✅ | ✅ | ✅ |
| Sitemap included | ✅ | ✅ | ✅ | ✅ | ✅ |

## Testing Tools

- **Google:** https://search.google.com/search-console
- **Bing:** https://www.bing.com/webmasters
- **Perplexity:** No verification portal — ensure JSON-LD is valid
- **ChatGPT Search:** Uses Bing index, so Bing Webmaster Tools is sufficient

## Common Mistakes

1. **Duplicate meta descriptions** — every page needs unique content
2. **Missing h1** — every page must have exactly one
3. **Relative URLs in canonical/og:url** — always use absolute
4. **JSON-LD syntax errors** — validate with https://validator.schema.org
5. **No sitemap** — AI engines need explicit URL discovery for static sites