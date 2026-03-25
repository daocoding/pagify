# Structured Data Patterns — Copy-Paste JSON-LD Templates

_Last verified: 2026-03-25_

## Purpose

The GEO guide explains _why_ structured data matters. This file gives you _exact templates_ to copy, fill in, and drop into any Pagify page. No schema expertise required.

Test every schema with [Google Rich Results Test](https://search.google.com/test/rich-results) before deploying.

---

## 1. WebSite (with SearchAction) — Home Page

Place in `<head>` of `src/_includes/layouts/base.njk`. Enables "Search on [site]" voice commands.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "WebSite",
  "name": "{{ site.title }}",
  "url": "{{ site.url }}",
  "description": "{{ site.description }}",
  "potentialAction": {
    "@type": "SearchAction",
    "target": {
      "@type": "EntryPoint",
      "urlTemplate": "{{ site.url }}/search?q={search_term_string}"
    },
    "query-input": "required name=search_term_string"
  }
}
</script>
```

> **Note:** The `/search` endpoint is optional. If your site doesn't have one, remove `potentialAction` entirely. A bare `WebSite` schema still provides entity clarity for AI engines.

---

## 2. Organization — Site-Wide (nav or footer)

Place once per page, typically in `<head>` via `base.njk`. Establishes the primary entity across all pages.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "Jane Smith Financial Coaching",
  "url": "https://janesmithcoaching.com",
  "logo": "https://janesmithcoaching.com/img/logo.png",
  "description": "Financial literacy workshops and one-on-one coaching for families in Raleigh, NC",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "123 Main Street, Suite 400",
    "addressLocality": "Raleigh",
    "addressRegion": "NC",
    "postalCode": "27601",
    "addressCountry": "US"
  },
  "telephone": "+1-919-555-0100",
  "email": "jane@janesmithcoaching.com",
  "sameAs": [
    "https://www.facebook.com/janesmithcoaching",
    "https://www.linkedin.com/in/janesmithcoaching"
  ]
}
</script>
```

**Fill-in checklist:**
- [ ] `name` — exactly as it appears on all pages
- [ ] `url` — your production domain (no `localhost`, no trailing slash)
- [ ] `logo` — absolute URL to your logo image
- [ ] `description` — one sentence, specific (not marketing copy)
- [ ] `address` — real address (required for local discovery)
- [ ] `telephone` — E.164 format preferred: `+1-XXX-XXX-XXXX`
- [ ] `email` — contact email
- [ ] `sameAs` — your real social profile URLs (at minimum, LinkedIn)

---

## 3. ProfessionalService — Services Pages

For pages describing coaching services. Use in addition to (not instead of) the Organization schema.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "ProfessionalService",
  "name": "Jane Smith Financial Coaching",
  "description": "One-on-one retirement planning and debt reduction coaching for families in the Triangle area of North Carolina",
  "url": "https://janesmithcoaching.com/services",
  "address": {
    "@type": "PostalAddress",
    "addressLocality": "Raleigh",
    "addressRegion": "NC",
    "addressCountry": "US"
  },
  "areaServed": [
    {
      "@type": "State",
      "name": "North Carolina"
    },
    {
      "@type": "City",
      "name": "Raleigh"
    },
    {
      "@type": "City",
      "name": "Durham"
    },
    {
      "@type": "City",
      "name": "Cary"
    }
  ],
  "serviceType": [
    "Retirement Planning",
    "Debt Reduction Coaching",
    "Financial Literacy Workshop"
  ],
  "priceRange": "$$",
  "telephone": "+1-919-555-0100",
  "openingHoursSpecification": [
    {
      "@type": "OpeningHoursSpecification",
      "dayOfWeek": ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday"],
      "opens": "09:00",
      "closes": "17:00"
    }
  ]
}
</script>
```

**Fill-in checklist:**
- [ ] `areaServed` — list cities and states you actually serve (be specific, not the whole world)
- [ ] `serviceType` — use standard names from [Schema.org Service](https://schema.org/Service)
- [ ] `priceRange` — `Free`, `$`, `$$`, `$$$`, or `$$$$` (not dollar signs, it's a schema convention)
- [ ] `openingHoursSpecification` — real hours; remove if you don't have fixed hours

---

## 4. Person — About / Bio Pages

For the coach's personal biography page.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Person",
  "name": "Jane Smith",
  "url": "https://janesmithcoaching.com/about",
  "description": "Licensed financial professional with 15 years of experience helping families in the Triangle area build retirement security and reduce debt",
  "image": "https://janesmithcoaching.com/img/jane-smith-headshot.jpg",
  "jobTitle": "Founder & Financial Coach",
  "worksFor": {
    "@type": "Organization",
    "name": "Jane Smith Financial Coaching",
    "url": "https://janesmithcoaching.com"
  },
  "address": {
    "@type": "PostalAddress",
    "addressLocality": "Raleigh",
    "addressRegion": "NC",
    "addressCountry": "US"
  },
  "telephone": "+1-919-555-0100",
  "email": "jane@janesmithcoaching.com",
  "sameAs": [
    "https://www.linkedin.com/in/janesmithcoaching"
  ],
  "knowsAbout": [
    "Retirement Planning",
    "Debt Reduction",
    "College Savings",
    "Financial Literacy"
  ]
}
</script>
```

---

## 5. FAQPage — FAQ Section

For pages with a dedicated FAQ section. List each real question clients ask — not marketing questions.

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
        "text": "Initial consultations are free. Ongoing coaching packages start at $200/month."
      }
    },
    {
      "@type": "Question",
      "name": "Do I need any financial background to start?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "No. Our workshops are designed for complete beginners. We start with the basics of budgeting and saving."
      }
    },
    {
      "@type": "Question",
      "name": "Do you offer virtual sessions?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes. All coaching sessions are available via Zoom. In-person sessions are available in Raleigh, NC."
      }
    },
    {
      "@type": "Question",
      "name": "What age groups do you work with?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "We primarily work with adults ages 25–65, from first-job beginners to pre-retirees planning their next chapter."
      }
    }
  ]
}
</script>
```

**Rules:**
- Write `acceptedAnswer.text` as a single sentence or two. No lists, no paragraphs.
- Ask only questions clients actually ask. Fake FAQs hurt rather than help.
- Maximum useful FAQs: 10–15. More than that and you're dilution, not depth.

---

## 6. Event — Workshop / Class Pages

For pages about workshops, classes, or community events.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Event",
  "name": "Retirement Planning Basics — Free Workshop",
  "description": "A 90-minute workshop covering how to start saving for retirement, even if you're starting from zero. Open to all community members.",
  "startDate": "2026-04-15T18:00:00-05:00",
  "endDate": "2026-04-15T19:30:00-05:00",
  "eventStatus": "https://schema.org/EventScheduled",
  "eventAttendanceMode": "https://schema.org/OfflineEventAttendanceMode",
  "location": {
    "@type": "Place",
    "name": "Raleigh Community Center",
    "address": {
      "@type": "PostalAddress",
      "streetAddress": "123 Main Street",
      "addressLocality": "Raleigh",
      "addressRegion": "NC",
      "postalCode": "27601",
      "addressCountry": "US"
    }
  },
  "organizer": {
    "@type": "Organization",
    "name": "Jane Smith Financial Coaching",
    "url": "https://janesmithcoaching.com"
  },
  "offers": {
    "@type": "Offer",
    "price": "0",
    "priceCurrency": "USD",
    "availability": "https://schema.org/InStock",
    "url": "https://janesmithcoaching.com/workshops/retirement-basics",
    "validFrom": "2026-03-01T00:00:00-05:00"
  }
}
</script>
```

---

## 7. BreadcrumbList — Interior Pages

For pages nested under sections (e.g., `/services/coaching/retirement/`). Helps AI understand site hierarchy.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  "itemListElement": [
    {
      "@type": "ListItem",
      "position": 1,
      "name": "Home",
      "item": "https://janesmithcoaching.com"
    },
    {
      "@type": "ListItem",
      "position": 2,
      "name": "Services",
      "item": "https://janesmithcoaching.com/services"
    },
    {
      "@type": "ListItem",
      "position": 3,
      "name": "Retirement Coaching",
      "item": "https://janesmithcoaching.com/services/retirement"
    }
  ]
}
</script>
```

---

## Implementation Pattern in Eleventy

### Step 1: Create `src/_data/structuredData.js`

```js
module.exports = {
  website: (site) => ({
    "@context": "https://schema.org",
    "@type": "WebSite",
    "name": site.title,
    "url": site.url,
    "description": site.description
  }),

  organization: (site) => ({
    "@context": "https://schema.org",
    "@type": "Organization",
    "name": site.title,
    "url": site.url,
    "description": site.description,
    "address: site.address",
    "telephone": site.telephone,
    "email": site.email,
    "sameAs": site.sameAs || []
  })
};
```

### Step 2: Include in `base.njk` `<head>`

```html
{% for schema in page.schemaData %}
<script type="application/ld+json">
{{ schema | dump | safe }}
</script>
{% endfor %}
```

### Step 3: Add per-page front matter

```markdown
---
title: About Jane Smith
description: Biography and background
layout: page.njk
schemaData:
  - "{{ site.data.structuredData.person | dump | safe }}"
---
```

---

## Testing Checklist

- [ ] Run every template through [Google Rich Results Test](https://search.google.com/test/rich-results) before going live
- [ ] Verify `url` fields use production domain (not `localhost:8080`)
- [ ] Verify `address.country` uses two-letter ISO code (US, not USA)
- [ ] Verify `priceRange` uses `$` notation, not "Free" (use `priceRange: "Free"` for free services)
- [ ] Verify no duplicate `@type` schemas on the same page
- [ ] Verify JSON-LD is valid JSON (run through [JSONLint](https://jsonlint.com) if unsure)

---

## Common Mistakes

| Mistake | Why it matters |
|---|---|
| Using `localhost` in `url` | AI engines can't index localhost; deploy to staging URL first |
| Adding fake FAQs | AI engines penalize irrelevant schema; only list real questions |
| Over-serializing `dump \| safe` | Eleventy's `dump` adds extra quotes; test output carefully |
| Missing `addressCountry` | Required for local discovery; without it, you're not locally findable |
| Multiple `Organization` blocks on one page | Confuses entity resolution; one site-wide `Organization` is enough |
| Using non-standard `serviceType` values | Use [Schema.org Service](https://schema.org/Service) types, not made-up labels |

---

## File Location

Place all structured data templates in:
```
knowledge-base/structured-data/
```

---

_Last reviewed: 2026-03-25_
