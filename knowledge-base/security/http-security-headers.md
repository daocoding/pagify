# HTTP Security Headers for Static Sites

_Last verified: 2026-03-20_

## Summary

Every Pagify site deployed to Cloudflare Pages should include security headers. These are free, take 5 minutes to set up, and protect site visitors from common web attacks. Without them, browsers allow behaviors that put users at risk — even on a purely static site.

## Why This Matters for Pagify

"It's just a static site — what could go wrong?"

Plenty:
- **Clickjacking** — someone embeds the coach's site in an iframe on a scam page, tricking visitors into clicking things they didn't intend
- **MIME sniffing** — browsers guess file types wrong, potentially executing uploaded content as scripts
- **Downgrade attacks** — HTTP links mixed with HTTPS allow interception
- **XSS via injected scripts** — if any user-generated content ever appears (comments, forms), lack of CSP means open season

Security headers don't replace good code practices. They're a safety net that costs nothing.

## The `_headers` File

Cloudflare Pages supports a `_headers` file in the build output. For Eleventy, create it in `src/` so it gets copied to `_site/`.

### `src/_headers`

```
/*
  X-Frame-Options: DENY
  X-Content-Type-Options: nosniff
  Referrer-Policy: strict-origin-when-cross-origin
  Permissions-Policy: camera=(), microphone=(), geolocation=(), payment=()
  X-XSS-Protection: 0
  Strict-Transport-Security: max-age=31536000; includeSubDomains
  Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline' https://static.cloudflareinsights.com; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:; font-src 'self'; connect-src 'self'; frame-ancestors 'none'; base-uri 'self'; form-action 'self'
```

### What Each Header Does

| Header | What It Prevents | Why It Matters |
|---|---|---|
| `X-Frame-Options: DENY` | Clickjacking — site can't be embedded in iframes | Stops scam sites from framing the coach's page |
| `X-Content-Type-Options: nosniff` | MIME type confusion | Browser won't guess file types — serves what you told it to |
| `Referrer-Policy: strict-origin-when-cross-origin` | Leaking full URLs to third parties | Sends only the origin (not full path) when navigating to external sites |
| `Permissions-Policy` | Browser API abuse | Explicitly denies camera, mic, geolocation, payment APIs the site doesn't use |
| `X-XSS-Protection: 0` | Disables legacy XSS filter | The old XSS Auditor caused more problems than it solved; CSP replaces it |
| `Strict-Transport-Security` | HTTPS downgrade attacks | Forces browsers to always use HTTPS for 1 year after first visit |
| `Content-Security-Policy` | XSS and injection attacks | Whitelists exactly which scripts, styles, and resources are allowed |

## Content-Security-Policy Breakdown

The CSP above is tuned for a standard Pagify site with Cloudflare Analytics:

```
default-src 'self'                    → Only load resources from the site's own origin
script-src 'self' 'unsafe-inline'     → Allow site scripts + inline scripts (Eleventy templates)
  https://static.cloudflareinsights.com → Allow Cloudflare Analytics beacon
style-src 'self' 'unsafe-inline'      → Allow site styles + inline styles
img-src 'self' data: https:           → Allow site images, data URIs, and any HTTPS image
font-src 'self'                       → Only self-hosted fonts
connect-src 'self'                    → AJAX/fetch only to own origin
frame-ancestors 'none'                → Same as X-Frame-Options: DENY (CSP version)
base-uri 'self'                       → Prevent base tag injection
form-action 'self'                    → Forms can only submit to own origin
```

### Adjusting CSP for Different Analytics Providers

**Plausible Analytics:**
```
script-src 'self' 'unsafe-inline' https://plausible.io;
connect-src 'self' https://plausible.io;
```

**Google Analytics 4:**
```
script-src 'self' 'unsafe-inline' https://www.googletagmanager.com https://www.google-analytics.com;
img-src 'self' data: https: https://www.google-analytics.com;
connect-src 'self' https://www.google-analytics.com https://analytics.google.com;
```

**No analytics:**
```
script-src 'self' 'unsafe-inline';
connect-src 'self';
```

## Eleventy Configuration

Add the `_headers` file to Eleventy's passthrough copy in `.eleventy.js`:

```js
module.exports = function(eleventyConfig) {
  eleventyConfig.addPassthroughCopy("src/css");
  eleventyConfig.addPassthroughCopy("src/images");
  eleventyConfig.addPassthroughCopy("src/_headers");  // ← add this

  return {
    dir: {
      input: "src",
      output: "_site",
      includes: "_includes",
      data: "_data"
    },
    markdownTemplateEngine: "njk"
  };
};
```

**Important:** The `_headers` file must end up at `_site/_headers` (root of the build output). The passthrough copy above handles this.

## Verification

### Quick check (after deploy):

```bash
curl -I https://your-site.pages.dev
```

Look for all the headers in the response. If any are missing, the `_headers` file isn't being picked up.

### Full scan:

- [securityheaders.com](https://securityheaders.com/) — enter the site URL, get a letter grade
- Target: **A** or **A+** grade

A stock Pagify site with the `_headers` file above should score **A**.

### Common failure modes:

| Symptom | Cause | Fix |
|---|---|---|
| Headers missing entirely | `_headers` not in build output | Add passthrough copy in `.eleventy.js` |
| Analytics script blocked | CSP doesn't include analytics domain | Add the provider's domain to `script-src` |
| Images broken | CSP `img-src` too restrictive | Add `https:` to allow external HTTPS images |
| Fonts broken | Using Google Fonts but CSP blocks them | Add `https://fonts.googleapis.com` to `style-src` and `https://fonts.gstatic.com` to `font-src` |
| Inline styles broken | CSP missing `'unsafe-inline'` in `style-src` | Add it (Eleventy templates commonly use inline styles) |

## For Coaches

When handing off a site:

1. Run the site URL through [securityheaders.com](https://securityheaders.com/)
2. Confirm grade is A or better
3. If anything scores below A, check that `_headers` exists in the deployed site

This is a 30-second check that dramatically improves the professionalism of a delivered site. Clients won't notice headers — but their clients' browsers will.

## For Agents (Scaffold Integration)

When scaffolding a new site (see [scaffold-new-site.md](../agent-patterns/scaffold-new-site.md)):

1. Create `src/_headers` with the template above
2. Add passthrough copy to `.eleventy.js`
3. Adjust CSP `script-src` based on the analytics provider in `site.json`
4. After first deploy, verify with `curl -I` or securityheaders.com
5. Add to the verification checklist: "Security headers present and score A+"

## References

- [Cloudflare Pages Headers](https://developers.cloudflare.com/pages/configuration/headers/)
- [MDN — Content-Security-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy)
- [MDN — Strict-Transport-Security](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security)
- [OWASP Secure Headers Project](https://owasp.org/www-project-secure-headers/)
- [securityheaders.com](https://securityheaders.com/)
