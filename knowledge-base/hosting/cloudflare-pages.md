# Cloudflare Pages — Hosting Evaluation

_Last verified: 2026-03-13_

## Summary

**Recommended as Pagify's default deployment target.** Free tier is genuinely free (no credit card), generous limits, and the platform aligns with Pagify's static-first architecture.

## Free Tier (as of March 2026)

| Feature | Limit |
|---|---|
| Sites | 100 projects |
| Builds | 500 per month |
| Bandwidth | Unlimited |
| Custom domains | Unlimited |
| SSL/TLS | Automatic, free |
| Preview deployments | Unlimited |
| Concurrent builds | 1 |
| Build timeout | 20 minutes |
| Max file size | 25 MB per file |
| Max files per project | 20,000 |

## Why It Fits Pagify

1. **No credit card required** — critical for non-technical users who are wary of surprise bills
2. **Unlimited bandwidth** — a static site for a local coach will never hit limits
3. **Git-based deploys** — push to GitHub, site updates automatically
4. **Preview deployments** — every PR gets a preview URL; coaches can review before going live
5. **Custom domains** — users bring their own domain; Cloudflare handles DNS + SSL
6. **Edge network** — 300+ locations; fast globally without configuration

## Deployment Flow (Eleventy)

```
1. Connect GitHub repo to Cloudflare Pages
2. Build command: npx @11ty/eleventy
3. Output directory: _site
4. Deploy on push to main
```

That's it. No Docker, no CI config files, no build servers.

## Tradeoffs

- **No server-side logic on free tier** — Functions (serverless) exist but have separate limits (100K requests/day free). Pagify sites shouldn't need this.
- **Build queue** — Free tier gets 1 concurrent build. If multiple people push at the same time, builds queue. Not a real problem for individual sites.
- **Vendor lock-in risk** — Low. Output is static HTML. Moving to any other static host (Netlify, GitHub Pages, Vercel) requires only changing the deploy target, not the code.
- **Cloudflare account required** — User needs a Cloudflare account. Free, but it's another account to manage.

## Comparison to Alternatives

| Platform | Free bandwidth | Credit card | Custom domain | Build from Git |
|---|---|---|---|---|
| **Cloudflare Pages** | Unlimited | No | Yes | Yes |
| GitHub Pages | 100 GB/mo | No | Yes | Yes (Actions) |
| Netlify | 100 GB/mo | No | Yes | Yes |
| Vercel | 100 GB/mo | No | Yes | Yes |

All four are viable. Cloudflare Pages wins on bandwidth limits and simplicity for static sites. GitHub Pages is the simplest if already on GitHub but has fewer features.

## Setup Guide for Coaches

> Detailed step-by-step guide: TODO (will be added as a separate doc with screenshots)

Quick version:
1. Sign up at [dash.cloudflare.com](https://dash.cloudflare.com) (free)
2. Go to Workers & Pages → Create → Pages → Connect to Git
3. Select the site's GitHub repo
4. Set build command: `npx @11ty/eleventy`
5. Set output directory: `_site`
6. Deploy

Custom domain:
1. Pages project → Custom domains → Add
2. Add CNAME record pointing to `<project>.pages.dev`
3. SSL activates automatically (takes a few minutes)

## References

- [Cloudflare Pages Docs](https://developers.cloudflare.com/pages/)
- [Cloudflare Pages Pricing](https://www.cloudflare.com/plans/developer-platform/)
- [Eleventy + Cloudflare Pages](https://developers.cloudflare.com/pages/framework-guides/deploy-an-eleventy-site/)
