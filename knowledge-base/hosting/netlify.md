# Netlify Deployment

[Netlify](https://www.netlify.com/) is a popular static hosting platform with a generous free tier and simple drag-and-drop or Git-based deployment.

## Free Tier Limits

- **Bandwidth:** 100 GB/month
- **Build minutes:** 500 minutes/month
- **Sites:** Unlimited
- **Team members:** 1 (free), more on paid plans
- **Custom domains:** Unlimited SSL (via Let's Encrypt)

## Why Choose Netlify

| Feature | Netlify | Cloudflare Pages |
|---------|---------|------------------|
| Drag-and-drop deploy | ✅ | ❌ |
| Forms (no backend) | ✅ (limited) | ❌ |
| Edge functions | ✅ | ✅ Workers |
| UI complexity | Higher (more options) | Lower |
| Git integration | ✅ | ✅ |

## Deployment Options

### Option 1: Drag and Drop

The simplest method for non-technical users:

1. Build your Pagify site: `npm run build`
2. Drag the `_site/` folder onto Netlify's deploy page
3. Netlify auto-detects Eleventy and deploys

**Downside:** No atomic deploys, harder to rollback.

### Option 2: Git Integration (Recommended)

Connect your GitHub repo for automatic builds on every push.

1. Push your Pagify repo to GitHub
2. Go to Netlify → "Add new site" → "Import an existing project"
3. Select your repo
4. Configure:
   - **Build command:** `npm run build`
   - **Publish directory:** `_site/`
5. Deploy

Netlify auto-detects Eleventy. If not, create `netlify.toml`:

```toml
[build]
  command = "npm run build"
  publish = "_site/"

[build.environment]
  NODE_VERSION = "18"
```

### Option 3: Netlify CLI

For command-line deployment:

```bash
npm install -g netlify-cli
netlify login
netlify deploy --prod --dir=_site
```

## Custom Domains

1. **Add domain:** Site settings → Domain management → Add custom domain
2. **SSL:** Auto-provisioned via Let's Encrypt (free)
3. **DNS:** Update your registrar's NS records to Netlify's nameservers

For `www` to non-www redirect, add `_redirects` in `_site/`:

```
/*    https://yoursite.com/:splat  301!
```

Netlify reads this at deploy time — add it to your Eleventy config as a passthrough copy.

## Forms (Limited)

Netlify's built-in form handling works with static sites:

```html
<form name="contact" netlify>
  <input type="text" name="name" required>
  <input type="email" name="email" required>
  <textarea name="message"></textarea>
  <button type="submit">Send</button>
</form>
```

**Limitations:**
- No file uploads
- No server-side validation
- Submissions capped at 100/month on free tier
- No confirmation emails (need external service)

For more robust forms, see [forms-and-contact.md](../architecture/forms-and-contact.md).

## Edge Functions

Netlify Edge Functions run JavaScript at the edge. Useful for:

- A/B testing
- Geo-based redirects
- Custom authentication

```bash
# Install
npm install @netlify/edge-functions
```

Not needed for most Pagify sites — static HTML suffices.

## Rollback

Netlify provides deploy history:

1. Deploys tab → find previous deploy
2. "Publish deploy" to rollback

Or use the CLI:
```bash
netlify deploy-history --siteID=YOUR_SITE_ID
```

## When NOT to Use Netlify

- **High bandwidth needs:** 100 GB/month fills fast with media sites
- **Form complexity:** Need FileUpload, PDF generation, or email notifications
- **Privacy concerns:** Netlify logs requests by default
- **Ejecting later:** Proprietary features like Edge Functions create lock-in

For these cases, consider Cloudflare Pages or self-hosting.

## Agent Pattern: Deploy a Site for a Coach

```bash
# 1. Clone or scaffold the site
git clone https://github.com/coach/client-site.git
cd client-site

# 2. Build locally to verify
npm run build

# 3. Login to Netlify (coach does this once)
netlify login

# 4. Create site (one-time)
netlify sites:create --name=coach-client-site

# 5. Link local repo
netlify link

# 6. Deploy
netlify deploy --prod --dir=_site

# 7. Configure custom domain (optional)
netlify domain:add customdomain.com
netlify ssl:create
```

## See Also

- [Cloudflare Pages](./cloudflare-pages.md) — alternative with Workers
- [Domain Registrars](./domain-registrars.md) — buying and managing domains
- [Deployment Pipeline](../agent-patterns/deploy-pipeline.md) — full CI/CD pattern