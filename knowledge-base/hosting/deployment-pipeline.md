# Deployment Pipeline: GitHub → Cloudflare Pages

How to set up automatic deployment so every push to `main` publishes the site. Zero cost, zero maintenance.

---

## Why This Matters

The biggest drop-off in the workshop-to-live-site pipeline is deployment. People build locally, it works, and then... nothing. They don't know how to get it on the internet, or the process feels fragile enough that they never push updates.

Automated deployment eliminates this entirely. Push code → site updates. That's it.

---

## Prerequisites

- GitHub repository with an Eleventy project (see [eleventy-project-structure.md](../architecture/eleventy-project-structure.md))
- Cloudflare account (free tier is sufficient — see [cloudflare-pages.md](cloudflare-pages.md))

---

## Option 1: Cloudflare Pages Git Integration (Recommended)

The simplest path. Cloudflare watches the repo and builds automatically.

### Setup

1. Log into [Cloudflare Dashboard](https://dash.cloudflare.com/) → **Workers & Pages** → **Create**
2. Select **Pages** → **Connect to Git**
3. Authorize Cloudflare to access the GitHub repo
4. Configure build settings:

| Setting | Value |
|---|---|
| Production branch | `main` |
| Build command | `npx @11ty/eleventy` |
| Build output directory | `_site` |
| Node.js version | `20` (set via environment variable `NODE_VERSION=20`) |

5. Click **Save and Deploy**

### What You Get

- **Auto-deploy on push**: every commit to `main` triggers a build
- **Preview deployments**: every PR gets its own preview URL (e.g., `abc123.pagify-site.pages.dev`)
- **Rollbacks**: one-click rollback to any previous deployment in the dashboard
- **Build logs**: visible in Cloudflare dashboard for debugging

### Custom Domain

After first deploy:
1. **Workers & Pages** → your project → **Custom domains** → **Set up a custom domain**
2. Enter your domain (e.g., `example.com`)
3. Cloudflare auto-configures DNS if the domain uses Cloudflare DNS
4. SSL is automatic and free

---

## Option 2: GitHub Actions + Wrangler (For More Control)

Use this when you need custom build steps, environment variables, or want the build to happen in GitHub's infrastructure.

### `.github/workflows/deploy.yml`

```yaml
name: Deploy to Cloudflare Pages

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      deployments: write
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Install dependencies
        run: npm ci

      - name: Build
        run: npx @11ty/eleventy

      - name: Deploy
        uses: cloudflare/wrangler-action@v3
        with:
          apiToken: ${{ secrets.CLOUDFLARE_API_TOKEN }}
          accountId: ${{ secrets.CLOUDFLARE_ACCOUNT_ID }}
          command: pages deploy _site --project-name=your-project-name
```

### Required Secrets

Set these in GitHub → **Settings** → **Secrets and variables** → **Actions**:

| Secret | Where to find it |
|---|---|
| `CLOUDFLARE_API_TOKEN` | Cloudflare Dashboard → **My Profile** → **API Tokens** → Create token with "Cloudflare Pages: Edit" permission |
| `CLOUDFLARE_ACCOUNT_ID` | Cloudflare Dashboard → any domain → **Overview** sidebar → **Account ID** |

### When to Use This Over Option 1

- Custom pre-build steps (data fetching, image optimization, sitemap generation)
- Build requires secrets/environment variables not available in Cloudflare's build environment
- You want build caching via GitHub Actions cache
- Multi-site monorepo where only one subpath should deploy

---

## For Volunteer Claws (Agent Deployment Pattern)

When a Claw (AI agent) maintains a site on behalf of a non-technical user:

1. **Agent commits to a branch** → opens a PR → preview URL auto-generates
2. **User reviews the preview** (just a link — no technical skill needed)
3. **User approves** → agent merges → production auto-deploys
4. **Rollback**: if anything goes wrong, the agent or user reverts from the Cloudflare dashboard

This workflow means the non-technical user only ever interacts with:
- A preview link ("does this look right?")
- A yes/no approval

Everything else is handled by the agent and the pipeline.

---

## Build Failures: Common Causes

| Symptom | Likely cause | Fix |
|---|---|---|
| `Cannot find module "@11ty/eleventy"` | Missing from `package.json` | `npm install --save-dev @11ty/eleventy` |
| Build succeeds but site is blank | Wrong output directory | Verify `_site` matches your `.eleventy.js` config |
| CSS/images missing on deployed site | Hardcoded `localhost` paths | Use relative paths (`/css/style.css`) not absolute |
| Build times out | Large image assets in repo | Move images to Cloudflare R2 or external CDN |
| Node version mismatch | Cloudflare defaults to Node 18 | Set `NODE_VERSION=20` in environment variables |

---

## Cost

| Component | Free tier limit | Typical small site usage |
|---|---|---|
| Cloudflare Pages builds | 500/month | 1-5/month |
| Bandwidth | Unlimited | N/A |
| Custom domains | Unlimited | 1-2 |
| Preview deployments | Unlimited | As needed |
| GitHub Actions minutes | 2,000/month (free repos) | <10/month |

For Pagify's target audience (small business/personal sites), this is permanently free.

---

## Checklist for Coaches

Before handing a site to the owner:

- [ ] Deployment pipeline is set up and tested (push → live in <2 min)
- [ ] Custom domain is configured with SSL
- [ ] Owner has been shown the preview URL workflow
- [ ] Owner knows where to see build status (Cloudflare dashboard or GitHub Actions tab)
- [ ] At least one test deployment has succeeded after the owner pushed a change (or approved a PR)
- [ ] Rollback procedure has been demonstrated

The goal: the owner should be able to say "my site updates automatically when I (or my Claw) make changes" — and actually believe it, because they've seen it work.
