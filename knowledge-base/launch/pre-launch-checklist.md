# Pre-Launch Checklist

Everything to verify before a site goes live. Designed for coaches walking non-technical people through their first deployment.

---

## Content

- [ ] All placeholder text replaced with real content
- [ ] Contact information is accurate (phone, email, address)
- [ ] Business hours are correct and timezone-explicit
- [ ] All internal links work (no broken hrefs)
- [ ] Images have descriptive `alt` text (see [accessibility essentials](../accessibility/web-accessibility-essentials.md))
- [ ] No lorem ipsum or TODO comments in source
- [ ] Copyright year is current
- [ ] Spelling and grammar reviewed

## SEO

- [ ] Every page has a unique `<title>` (50–60 characters)
- [ ] Every page has a unique `<meta name="description">` (150–160 characters)
- [ ] Heading hierarchy is correct (`h1` → `h2` → `h3`, one `h1` per page)
- [ ] Canonical URLs set on all pages
- [ ] `robots.txt` exists and allows crawling
- [ ] `sitemap.xml` generated and submitted
- [ ] Open Graph tags present for social sharing
- [ ] See [meta tags checklist](../seo/meta-tags-checklist.md) for full details

## GEO (Generative Engine Optimization)

- [ ] Structured data (JSON-LD) on key pages — at minimum: Organization, LocalBusiness, or Person
- [ ] FAQ sections use `FAQPage` schema where applicable
- [ ] Content answers questions directly (not just keyword-stuffed)
- [ ] See [GEO guide](../geo/generative-engine-optimization.md) and [structured data patterns](../structured-data/structured-data-patterns.md)

## Performance

- [ ] Images optimized (WebP/AVIF, appropriately sized) — see [image optimization](../performance/image-optimization.md)
- [ ] No uncompressed images over 200KB
- [ ] CSS is minified in production build
- [ ] Fonts loaded with `font-display: swap`
- [ ] Lighthouse Performance score ≥ 90

## Security

- [ ] HTTPS enforced (redirect HTTP → HTTPS)
- [ ] Security headers configured — see [HTTP security headers](../security/http-security-headers.md)
- [ ] No API keys or secrets in client-side code
- [ ] Contact forms use CAPTCHA or honeypot spam protection
- [ ] Form submissions go to a working endpoint

## Accessibility

- [ ] Site navigable by keyboard alone (Tab, Enter, Escape)
- [ ] Color contrast ratio ≥ 4.5:1 for body text
- [ ] Interactive elements have visible focus styles
- [ ] Skip-to-content link present
- [ ] Lighthouse Accessibility score ≥ 90
- [ ] See [accessibility essentials](../accessibility/web-accessibility-essentials.md)

## Legal & Compliance

- [ ] Privacy policy page exists (required if collecting any data — forms, analytics, cookies)
- [ ] Cookie consent banner if using analytics or third-party scripts
- [ ] Terms of service page if offering services or products
- [ ] Business registration / licensing disclosures where legally required

## DNS & Domain

- [ ] Custom domain connected and resolving
- [ ] `www` and apex domain both work (one redirects to the other)
- [ ] SSL certificate active and auto-renewing
- [ ] Old domain redirects configured (if migrating from another site)
- [ ] See [domain registrars](../hosting/domain-registrars.md)

## Analytics

- [ ] Analytics installed and receiving data
- [ ] Privacy-respecting option chosen (see [analytics options](../hosting/analytics-options.md))
- [ ] Goal/conversion tracking configured if applicable
- [ ] Analytics verified in a real browser (not just dev tools)

## Deployment

- [ ] Production build runs without errors (`npx @11ty/eleventy`)
- [ ] Build output deployed to hosting provider
- [ ] Custom 404 page configured
- [ ] Deployment pipeline documented — see [deployment pipeline](../hosting/deployment-pipeline.md)
- [ ] Rollback plan: can you redeploy the previous version in under 5 minutes?

## Final Smoke Test

Run these checks from a device that isn't yours (phone, incognito window, a friend's computer):

- [ ] Homepage loads in under 3 seconds
- [ ] Navigation works on mobile
- [ ] Contact form submits successfully
- [ ] All pages render correctly (no layout breaks)
- [ ] Social sharing preview looks correct (paste URL into Twitter/LinkedIn/iMessage)

---

## For Coaches

Walk through this checklist with your client before going live. It takes 15–20 minutes and prevents the most common launch-day problems.

**Tip:** Copy this file into the client's project repo as `LAUNCH-CHECKLIST.md` so they can check items off themselves. Ownership starts before launch, not after.
