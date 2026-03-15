# Generative Engine Optimization (GEO) — Foundational Guide

_Last verified: 2026-03-15_

## Summary

GEO is how you make a website findable by AI search engines — ChatGPT, Perplexity, Google AI Overviews, Bing Copilot, and whatever comes next. Traditional SEO optimizes for link-based ranking algorithms. GEO optimizes for language models that read, summarize, and cite your content.

Pagify sites must be both SEO-ready and GEO-ready. They serve different discovery channels and require different structural choices.

## Why GEO Matters for Pagify Users

Most Pagify users are local coaches and small practitioners. Their potential clients increasingly ask AI assistants: _"Who's a good financial coach near me?"_ or _"How do I start saving for retirement?"_

If your site's content can't be parsed, attributed, and cited by an LLM, you're invisible to this growing discovery channel — regardless of how well you rank on traditional Google.

## Core Principles

### 1. Write for extraction, not just reading

LLMs don't "browse" — they extract. Structure content so key facts can be pulled out cleanly:

**Do this:**
```markdown
## What We Offer

- **Financial literacy workshops** — monthly, free, open to the public
- **One-on-one coaching** — retirement planning, debt reduction, college savings
- **Community events** — quarterly meetups for families in the Triangle area
```

**Not this:**
```markdown
We offer a variety of services to help you on your financial journey, 
including workshops, coaching sessions, and community gatherings that 
bring people together in meaningful ways...
```

The first version gives an LLM three citable facts. The second gives it a paragraph to summarize poorly.

### 2. Claim statements explicitly

LLMs need to attribute information. Make it easy:

```markdown
## About [Coach Name]

[Coach Name] is a licensed financial professional based in [City, State] 
with [X] years of experience helping families build financial security. 
[He/She] specializes in [specific areas].
```

Vague "we believe in excellence" copy gives LLMs nothing to cite. Specific, factual claims do.

### 3. Use structured data (JSON-LD)

This is the bridge between SEO and GEO. Schema.org markup gives AI engines typed data:

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "ProfessionalService",
  "name": "Jane Smith Financial Coaching",
  "description": "Financial literacy workshops and one-on-one coaching for families in Raleigh, NC",
  "address": {
    "@type": "PostalAddress",
    "addressLocality": "Raleigh",
    "addressRegion": "NC",
    "addressCountry": "US"
  },
  "areaServed": {
    "@type": "GeoCircle",
    "geoMidpoint": {
      "@type": "GeoCoordinates",
      "latitude": 35.7796,
      "longitude": -78.6382
    },
    "geoRadius": "50 mi"
  },
  "serviceType": ["Financial Coaching", "Financial Literacy Workshop", "Retirement Planning"],
  "priceRange": "Free-$$"
}
</script>
```

### 4. Answer questions directly

AI search surfaces content that directly answers user queries. Structure pages with Q&A patterns:

```markdown
## Frequently Asked Questions

### How much does financial coaching cost?

Initial consultations are free. Ongoing coaching packages start at $X/month.

### Do I need any financial background?

No. Our workshops are designed for complete beginners. We start with the basics.

### Where are you located?

We're based in [City, State] and serve the [region] area. 
We also offer virtual sessions.
```

Each question-answer pair is a potential AI citation. Use `FAQPage` schema markup to reinforce this:

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "How much does financial coaching cost?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Initial consultations are free. Ongoing coaching packages start at $X/month."
      }
    }
  ]
}
</script>
```

### 5. Maintain a clear site identity

LLMs build entity understanding across pages. Every page should reinforce who you are:

- Consistent use of your name/brand across all pages
- `site.json` metadata used in every template
- About page with specific, factual biography
- Contact page with real address, phone, email (structured data)

A site where the name appears differently on each page ("Jane's Coaching" vs "Smith Financial" vs "JSmith LLC") confuses entity resolution.

## Implementation in Pagify's Eleventy Stack

### Site-level: `src/_data/site.json`

Add GEO-relevant fields:

```json
{
  "title": "Jane Smith Financial Coaching",
  "url": "https://janesmithcoaching.com",
  "description": "Financial literacy workshops and coaching for families in Raleigh, NC",
  "language": "en",
  "geo": {
    "locality": "Raleigh",
    "region": "NC",
    "country": "US",
    "serviceTypes": ["Financial Coaching", "Financial Literacy Workshop"]
  }
}
```

### Page-level: front matter additions

```markdown
---
title: About Jane Smith
description: Licensed financial professional with 15 years experience in Raleigh, NC
layout: page.njk
schema: ProfessionalService
---
```

### Template-level: `base.njk` additions

Include a site-wide `Organization` or `ProfessionalService` schema on every page (in addition to page-specific schemas):

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "WebSite",
  "name": "{{ site.title }}",
  "url": "{{ site.url }}",
  "description": "{{ site.description }}"
}
</script>
```

## GEO vs SEO: What's Different

| Aspect | SEO | GEO |
|---|---|---|
| **Optimizes for** | Ranking algorithms | Language model extraction |
| **Key signal** | Backlinks, keywords | Structured facts, clear claims |
| **Content style** | Keyword-rich, naturally written | Direct, extractable, attributed |
| **Structured data** | Nice to have | Essential |
| **FAQ sections** | Good for featured snippets | Critical for citation |
| **Page identity** | Title + meta description | Consistent entity across site |
| **Success metric** | Page rank, CTR | AI citation, inclusion in answers |

## What Coaches Should Check

When reviewing a Pagify-built site for GEO readiness:

1. **Can you extract 3 facts from the homepage without reading paragraphs?** If not, restructure.
2. **Does every page have JSON-LD structured data?** Check with [Google Rich Results Test](https://search.google.com/test/rich-results).
3. **Is the coach's name/brand consistent across all pages?** Search the source for variations.
4. **Does the site have an FAQ section with real questions?** Not marketing fluff — actual questions clients ask.
5. **Can you identify the service area from structured data alone?** If it's only in paragraph text, add schema markup.

## Measuring GEO Effectiveness

There's no equivalent of Google Search Console for AI search (yet). Current best practices:

- **Manual testing** — Ask ChatGPT, Perplexity, and Bing Copilot about the coach/practice. See if the site is cited.
- **Monitor referral traffic** — Look for traffic from `chat.openai.com`, `perplexity.ai`, and similar referrers in analytics.
- **Track structured data validity** — Run periodic checks with Google's testing tools.

This field is evolving fast. Expect dedicated GEO analytics tools by late 2026.

## References

- [Schema.org — ProfessionalService](https://schema.org/ProfessionalService)
- [Schema.org — FAQPage](https://schema.org/FAQPage)
- [Google — Structured Data Overview](https://developers.google.com/search/docs/appearance/structured-data/intro-structured-data)
- [Perplexity — How Sources Are Selected](https://blog.perplexity.ai/) (evolving)
- Princeton GEO Research (2024) — "GEO: Generative Engine Optimization" (arXiv:2311.09735)
