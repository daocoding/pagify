# Multilingual Static Sites with Eleventy

> Last verified: 2026-03-24

## Why This Matters for Pagify

Pagify serves coaches working with multilingual communities — Chinese, Vietnamese, Spanish, Korean, and more. Many end beneficiaries are more comfortable in their native language. A site that only speaks English loses them.

This guide covers how to build multilingual static sites with Eleventy — real pages per language, proper SEO signals, no JavaScript frameworks required.

---

## Architecture: One URL Per Language

The canonical pattern for multilingual static sites:

```
/en/about/          → English
/zh/about/          → Chinese (Simplified)
/es/about/          → Spanish
/vi/about/          → Vietnamese
```

Each language gets its own directory and its own rendered HTML page. No client-side language switching. Every page is crawlable and indexable independently.

### Why Not Query Params or Cookies?

- `?lang=zh` — search engines may treat it as duplicate content
- Cookie-based switching — invisible to crawlers, bad for SEO/GEO
- JavaScript-based switching — fails without JS, invisible to AI engines

**Real pages > dynamic switching.** Always.

---

## Eleventy Implementation

### Directory Structure

```
src/
├── en/
│   ├── en.json           # Front matter defaults for English
│   ├── index.md
│   ├── about.md
│   └── services.md
├── zh/
│   ├── zh.json           # Front matter defaults for Chinese
│   ├── index.md
│   ├── about.md
│   └── services.md
└── _includes/
    └── base.njk          # Shared layout
```

### Language Data Files

`src/en/en.json`:
```json
{
  "locale": "en",
  "lang": "en",
  "dir": "ltr"
}
```

`src/zh/zh.json`:
```json
{
  "locale": "zh-Hans",
  "lang": "zh",
  "dir": "ltr"
}
```

These cascade to all pages in the directory — no need to repeat in each file's front matter.

### Base Layout with Language Attributes

`src/_includes/base.njk`:
```html
<!DOCTYPE html>
<html lang="{{ lang }}" dir="{{ dir | default('ltr') }}">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>{{ title }}</title>
  <link rel="alternate" hreflang="en" href="/en{{ page.url | replace(lang ~ '/', '') }}">
  <link rel="alternate" hreflang="zh-Hans" href="/zh{{ page.url | replace(lang ~ '/', '') }}">
  <!-- Add more hreflang links for each supported language -->
</head>
<body>
  {{ content | safe }}
</body>
</html>
```

---

## SEO: `hreflang` Tags

Search engines use `hreflang` to understand which page serves which language/region. Every translated page must link to all its alternates, including itself.

```html
<link rel="alternate" hreflang="en" href="https://example.com/en/about/">
<link rel="alternate" hreflang="zh-Hans" href="https://example.com/zh/about/">
<link rel="alternate" hreflang="x-default" href="https://example.com/en/about/">
```

- `x-default` = fallback for users whose language doesn't match any alternate
- Every page links to ALL alternates (including itself)
- Use full URLs, not relative paths

### Common Mistakes

| Mistake | Impact |
|---|---|
| Missing `hreflang` on some pages | Search engines can't connect translations |
| Using `zh` instead of `zh-Hans` | Doesn't distinguish Simplified from Traditional Chinese |
| Forgetting `x-default` | No fallback for unmatched languages |
| Only adding hreflang in one direction | Must be bidirectional — every page links to every alternate |

---

## GEO: Structured Data for AI Engines

AI search engines benefit from explicit language signals in structured data:

```json
{
  "@context": "https://schema.org",
  "@type": "WebPage",
  "inLanguage": "zh-Hans",
  "url": "https://example.com/zh/about/",
  "isPartOf": {
    "@type": "WebSite",
    "url": "https://example.com/",
    "availableLanguage": ["en", "zh-Hans", "es", "vi"]
  }
}
```

This tells AI engines: this page is in Simplified Chinese, and the site also exists in English, Spanish, and Vietnamese.

---

## Content Workflow for Coaches

Pagify's multilingual workflow for non-technical coaches:

1. **Write content in primary language** (usually the coach's native language)
2. **AI agent translates** to target languages — but preserves cultural nuance, not just words
3. **Coach reviews translations** (or a bilingual community member does)
4. **Agent creates the pages** in the correct directory structure
5. **Agent adds hreflang tags** automatically

### Translation Quality Rules

- **Names stay in original script** — 张伟 stays 张伟, not "Zhang Wei" (unless the person prefers romanization)
- **Cultural references get adapted**, not literally translated
- **Financial/legal terms** use the accepted term in each market — don't invent translations
- **Keep it natural** — a Chinese page should read like it was written in Chinese, not translated from English

---

## Root URL: Language Detection vs. Default

Two options for what happens at `/`:

### Option A: Redirect to default language
```
/ → 301 → /en/
```
Simple. Predictable. SEO-safe. **Recommended for Pagify.**

### Option B: Browser language detection
```
/ → detect Accept-Language header → redirect to matching language
```
Requires a Cloudflare Worker or similar edge function. More complex. Can confuse crawlers if not implemented carefully.

**For Pagify: start with Option A.** Add detection later if needed.

---

## Font Considerations

CJK (Chinese/Japanese/Korean) fonts are large. Don't load them for English-only visitors.

```html
{% if lang == "zh" %}
<link rel="preload" href="/fonts/noto-sans-sc-regular.woff2" as="font" type="font/woff2" crossorigin>
{% endif %}
```

Recommended free CJK fonts:
- **Noto Sans SC** — Google's Simplified Chinese, clean and professional
- **Noto Sans TC** — Traditional Chinese variant
- **Noto Sans JP/KR** — Japanese and Korean

Use `font-display: swap` to prevent invisible text during load.

---

## Tradeoffs

| Approach | Pros | Cons |
|---|---|---|
| Directory-per-language (`/en/`, `/zh/`) | SEO-clean, simple, each page independent | Content duplication across dirs |
| Subdomain (`en.example.com`) | Clear separation | More DNS config, separate deployments |
| Single-page with JS switching | Easy to build | Terrible for SEO/GEO, breaks without JS |

**Pagify's choice: directory-per-language.** It aligns with the anti-SPA philosophy and gives every language its own real, crawlable pages.

---

## Checklist

- [ ] Each language has its own directory under `src/`
- [ ] Language data file (`.json`) in each language directory
- [ ] `<html lang="...">` set correctly per page
- [ ] `hreflang` tags on every page, linking to all alternates
- [ ] `x-default` hreflang pointing to the primary language
- [ ] Structured data includes `inLanguage` and `availableLanguage`
- [ ] CJK fonts conditionally loaded only for CJK pages
- [ ] Root URL (`/`) redirects to default language
- [ ] Translation quality reviewed by native speaker before publish
