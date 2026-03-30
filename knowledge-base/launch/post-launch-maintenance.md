# Post-Launch Maintenance Guide

_Last verified: 2026-03-30_

Everything to keep a Pagify site healthy after it goes live. Designed for coaches helping non-technical site owners maintain their own sites — or for agents handling ongoing maintenance.

---

## The First 72 Hours

These checks happen once, right after launch:

- [ ] **Verify indexing request submitted** — use [Google Search Console](https://search.google.com/search-console) to request indexing of your sitemap
- [ ] **Confirm analytics is collecting data** — check for at least 1 real pageview (not your own)
- [ ] **Test contact form from a non-owner device** — submit a real test message and confirm delivery
- [ ] **Check social sharing previews** — paste your URL into Twitter, LinkedIn, iMessage, WhatsApp — does the card render correctly?
- [ ] **Verify structured data is live** — run homepage and contact page through [Google Rich Results Test](https://search.google.com/test/rich-results)
- [ ] **Monitor for crawl errors** — check Search Console for 404s, redirect issues, or blocked resources

---

## Monthly Maintenance (15 minutes)

A coach or site owner should do these every month. If an agent handles it, use the [site health monitoring pattern](../agent-patterns/site-health-monitoring.md).

### Content freshness

- [ ] **Business hours still accurate?** — holiday hours, seasonal changes, closures
- [ ] **Contact info still correct?** — phone number, email, physical address
- [ ] **Services list still current?** — remove discontinued services, add new ones
- [ ] **Testimonials or case studies to add?** — fresh social proof improves trust and GEO signals
- [ ] **Blog or news section?** — even one post per month signals an active site to search engines

### Technical health

- [ ] **Run a broken link check** — use a free tool like [Dr. Link Check](https://www.drlinkcheck.com/) or `npx broken-link-checker <your-url>`
- [ ] **Check page speed** — run [PageSpeed Insights](https://pagespeed.web.google.com/) on your homepage; score should stay ≥ 90
- [ ] **Review analytics** — which pages get traffic? Which don't? Low-traffic pages may need better content or internal links
- [ ] **Check SSL certificate** — should auto-renew on Cloudflare Pages, but verify the padlock icon is present

### Security

- [ ] **Review form submissions** — any spam getting through? Tighten CAPTCHA or honeypot if needed
- [ ] **Check for dependency updates** — run `npm outdated` and update Eleventy if a new stable version exists
- [ ] **Scan security headers** — use [SecurityHeaders.com](https://securityheaders.com/) to verify headers haven't regressed

---

## Quarterly Review (30 minutes)

### Search performance

- [ ] **Check Search Console** — are your pages being indexed? Any manual actions or warnings?
- [ ] **Review search queries** — what terms are people finding you for? Do they match your intent?
- [ ] **Check competitor sites** — have similar businesses in your area improved their sites? What can you learn?

### Content expansion

- [ ] **Add a new FAQ entry** — real questions from clients since last quarter; each FAQ = a GEO target
- [ ] **Update structured data** — add new services, reviews, or events to your JSON-LD
- [ ] **Review `<meta description>` tags** — do they still accurately describe each page's current content?

### Technical debt

- [ ] **Eleventy version check** — run `npm outdated @11ty/eleventy` and review changelog before upgrading
- [ ] **Image audit** — any new images added without optimization? Run through the [image optimization guide](../performance/image-optimization.md)
- [ ] **CSS audit** — any unused styles accumulating? Keep the stylesheet lean

---

## Annual Tasks

- [ ] **Update copyright year** — in footer and any legal pages
- [ ] **Review privacy policy** — still accurate for your current data practices?
- [ ] **Domain renewal** — verify auto-renew is enabled; check expiration date
- [ ] **Full accessibility audit** — run [Lighthouse](https://developers.google.com/web/tools/lighthouse) and fix any regressions
- [ ] **Content audit** — read every page start to finish; remove outdated info, refresh stale sections
- [ ] **Backup verification** — confirm your Git repo has all current content; test a fresh `git clone` + `npm run build`

---

## When to Call the Coach (or Agent)

The site owner should handle monthly maintenance themselves — that's the whole point of Pagify. But some things need help:

| Situation | Action |
|---|---|
| Build fails after an update | Roll back (`git checkout HEAD~1`), then ask for help |
| Google Search Console shows "manual action" | This is serious — contact your coach immediately |
| Site is hacked or defaced | Take it offline, restore from Git, review security headers |
| Major redesign or new section needed | Plan with your coach before making big structural changes |
| Domain or DNS issues | Don't guess — wrong DNS changes can take 48h to fix |

---

## For Agents: Automated Maintenance Checklist

If an agent is maintaining the site, run these checks on a schedule:

```bash
# Build verification (should exit 0)
npm run build

# Broken link check
npx broken-link-checker http://localhost:8080 --recursive

# Dependency check
npm outdated

# HTML validation (optional but good)
npx html-validate _site/**/*.html
```

See the [site health monitoring agent pattern](../agent-patterns/site-health-monitoring.md) for a full automated playbook.

---

## The Most Common Post-Launch Mistake

**Abandonment.** The site goes live, everyone celebrates, and then nothing happens for 6 months. The business hours become wrong. The contact form breaks. A dependency gets a security patch that never gets applied.

A static site is low-maintenance, not no-maintenance. 15 minutes a month keeps it alive.

---

## References

- [Pre-Launch Checklist](./pre-launch-checklist.md) — what to verify before going live
- [Site Health Monitoring Agent Pattern](../agent-patterns/site-health-monitoring.md)
- [Image Optimization Guide](../performance/image-optimization.md)
- [SEO Meta Tags Checklist](../seo/meta-tags-checklist.md)
- [HTTP Security Headers](../security/http-security-headers.md)
