# Web Analytics — Free Options for Coaches

_Last verified: 2026-03-18_

## Summary

Every Pagify site needs analytics. Without data, coaches can't tell if a site is actually reaching people. But most analytics tools are either privacy-hostile, complicated, or expensive. This guide evaluates free-tier options that coaches can confidently recommend to non-technical clients.

## What Coaches Actually Need to Know

Four questions. That's it:

1. **Is anyone visiting?** (unique visitors / month)
2. **How are they finding us?** (referral sources — Google, AI search, social, direct)
3. **What are they looking at?** (top pages)
4. **Are they taking action?** (contact page views, form submissions)

Everything else is noise for a local coach's site.

## Recommendations

### Tier 1: Best Default — Cloudflare Web Analytics

**Why it's the default for Pagify sites on Cloudflare Pages:**

- **Free forever** — no limits, no sampling, no premium tier needed
- **Privacy-first** — no cookies, no personal data collection, GDPR compliant out of the box
- **No consent banner needed** — because it doesn't track individuals
- **Automatic** — enabled in the Cloudflare dashboard, one toggle
- **Lightweight** — single JS snippet, ~5KB, no performance impact

**Setup:**
1. Cloudflare Dashboard → Web Analytics → Add Site
2. Copy the JS snippet into `base.njk` before `</body>`
3. Done

**Limitations:**
- No real-time data (updates every few hours)
- No event tracking (can't track button clicks or form submissions natively)
- No custom dashboards
- Data retention: 6 months on free plan

**Verdict:** If the site is on Cloudflare Pages (Pagify's default), this is the obvious choice. Zero cost, zero privacy issues, zero configuration complexity.

### Tier 2: More Detail — Plausible Analytics (Community Edition)

**Why consider it:**

- **Open source** — self-hostable, MIT-licensed community edition
- **Privacy-first** — no cookies, no personal data, GDPR/CCPA compliant
- **Better dashboard** — real-time, goal tracking, custom events
- **UTM tracking** — see which campaigns drive traffic
- **AI search referrals** — shows traffic from `chat.openai.com`, `perplexity.ai`, etc. (critical for GEO measurement)

**Free path:** Self-host the Community Edition on any VPS ($5/month for a small Hetzner/DigitalOcean instance). One instance can serve dozens of sites.

**Paid cloud:** $9/month for hosted Plausible. Not free, but worth knowing about.

**Setup (self-hosted):**
1. Follow [Plausible Community Edition docs](https://github.com/plausible/community-edition)
2. Docker Compose on any Linux VPS
3. Add `<script>` tag to `base.njk`

**Setup (cloud):**
1. Sign up at plausible.io
2. Add site domain
3. Add `<script>` tag to `base.njk`

**Limitations (self-hosted):**
- Requires maintaining a server (updates, uptime)
- Not realistic for coaches who aren't technical
- Better suited for a Volunteer Claw or community hub operator

**Verdict:** Best option when coaches need event tracking or AI search referral data. The self-hosted path is free but requires a technical operator. Cloud path is simple but not free.

### Tier 3: Maximum Features — Google Analytics 4 (GA4)

**Why it exists in this list:**

- **Free** — genuinely free for the volume these sites generate
- **Most powerful** — conversion tracking, audience segments, integrations with Google Ads and Search Console
- **Industry standard** — most online resources assume GA4

**Why it's Tier 3 (not recommended as default):**

- **Cookie consent required** — GDPR/CCPA mandate a consent banner. This adds complexity and hurts user experience.
- **Privacy concerns** — data is used by Google for ad targeting. Some coaches' clients will care.
- **Complexity** — the dashboard is built for marketing teams, not for someone who wants to know "is anyone visiting?"
- **Heavy** — the tracking script impacts page load performance
- **Data ownership** — Google owns the infrastructure. You can export, but you don't control the platform.

**When to use it anyway:**
- Coach is already using Google Ads and needs conversion tracking
- Integration with Google Search Console is a priority
- The client specifically requests it

**Setup:**
1. Create a Google Analytics account
2. Create a GA4 property
3. Add the `gtag.js` snippet to `base.njk`
4. **Add a cookie consent banner** (required for compliance)

**Verdict:** Powerful but heavy. Only recommend when specific Google integrations are needed.

## What NOT to Recommend

| Tool | Why Not |
|---|---|
| **Matomo (hosted)** | Free tier is too limited; self-hosted is complex; cookie-based by default |
| **Fathom** | Good tool, but $15/month minimum. No free tier. |
| **Heap / Mixpanel / Amplitude** | Enterprise-grade. Overkill. Will confuse non-technical users. |
| **No analytics at all** | Tempting, but a site without analytics is a site that can't improve. Coaches need to know if their work is landing. |

## Implementation in Pagify Templates

### The `base.njk` Pattern

Analytics scripts go at the end of `<body>`, controlled by site config:

```html
{# Analytics — before </body> #}
{% if site.analytics.provider == "cloudflare" %}
  <script defer src='https://static.cloudflareinsights.com/beacon.min.js'
    data-cf-beacon='{"token": "{{ site.analytics.token }}"}'></script>
{% elif site.analytics.provider == "plausible" %}
  <script defer data-domain="{{ site.analytics.domain }}"
    src="{{ site.analytics.src | default('https://plausible.io/js/script.js') }}"></script>
{% elif site.analytics.provider == "ga4" %}
  <script async src="https://www.googletagmanager.com/gtag/js?id={{ site.analytics.id }}"></script>
  <script>
    window.dataLayer = window.dataLayer || [];
    function gtag(){dataLayer.push(arguments);}
    gtag('js', new Date());
    gtag('config', '{{ site.analytics.id }}');
  </script>
{% endif %}
```

### The `site.json` Config

```json
{
  "analytics": {
    "provider": "cloudflare",
    "token": "your-cf-analytics-token"
  }
}
```

Agents scaffolding a new site should default to `cloudflare` if the site is deployed on Cloudflare Pages, or `plausible` if self-hosted elsewhere.

## For Coaches: The 5-Minute Check

Every month, spend 5 minutes answering these questions from your analytics dashboard:

1. **Visitors trending up or down?** If down for 2+ months, something needs attention.
2. **Where is traffic coming from?** If it's all direct, SEO/GEO work is needed.
3. **Is the contact/about page getting views?** If not, the homepage isn't doing its job.
4. **Any traffic from AI search (Perplexity, ChatGPT)?** This tells you if GEO is working.

That's the whole review. Don't overcomplicate it.

## References

- [Cloudflare Web Analytics](https://www.cloudflare.com/web-analytics/)
- [Plausible Community Edition](https://github.com/plausible/community-edition)
- [Plausible Cloud](https://plausible.io/)
- [Google Analytics 4](https://analytics.google.com/)
- [GDPR Cookie Consent Requirements](https://gdpr.eu/cookies/)
