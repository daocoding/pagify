# Agent Pattern: Deploy Pipeline

> An agent-driven deployment workflow for static sites — build, validate, deploy, verify.

## When to Use

- Deploying static site updates to hosting (Cloudflare Pages, Netlify, GitHub Pages, S3)
- CI/CD-free workflows where an agent replaces the pipeline
- Sites that need pre-deploy validation (link checks, HTML validity, asset verification)

## Pattern

### 1. Pre-Deploy Validation

Before deploying, the agent runs local checks:

```bash
# Validate HTML
find . -name "*.html" -exec html-validate {} +

# Check for broken internal links
grep -roh 'href="[^"]*"' *.html | sort -u | while read link; do
  target=$(echo "$link" | sed 's/href="//;s/"//')
  # Skip external links and anchors
  [[ "$target" == http* ]] && continue
  [[ "$target" == \#* ]] && continue
  [[ ! -f "$target" ]] && echo "BROKEN: $target"
done

# Verify all referenced assets exist
grep -roh 'src="[^"]*"' *.html | sort -u | while read src; do
  target=$(echo "$src" | sed 's/src="//;s/"//')
  [[ "$target" == http* ]] && continue
  [[ ! -f "$target" ]] && echo "MISSING ASSET: $target"
done

# Check file sizes (flag anything over 5MB)
find . -type f -size +5M -exec ls -lh {} +
```

### 2. Deploy by Platform

#### Cloudflare Pages (Direct Upload)

```bash
# Using wrangler
npx wrangler pages deploy ./dist --project-name=my-site

# Or via git push (if connected to repo)
git add -A && git commit -m "deploy: update content" && git push
```

#### GitHub Pages

```bash
# Push to gh-pages branch
git subtree push --prefix dist origin gh-pages

# Or use gh-pages npm package
npx gh-pages -d dist
```

#### S3 + CloudFront

```bash
aws s3 sync ./dist s3://my-bucket --delete
aws cloudfront create-invalidation --distribution-id XXXX --paths "/*"
```

#### Netlify

```bash
npx netlify deploy --prod --dir=dist
```

### 3. Post-Deploy Verification

After deploy, verify the live site:

```bash
# Check HTTP status
curl -s -o /dev/null -w '%{http_code}' https://mysite.com

# Verify key pages load
for page in "/" "/about" "/contact"; do
  status=$(curl -s -o /dev/null -w '%{http_code}' "https://mysite.com${page}")
  echo "${page}: ${status}"
done

# Check SSL certificate validity
echo | openssl s_client -connect mysite.com:443 2>/dev/null | openssl x509 -noout -dates

# Verify cache headers
curl -sI https://mysite.com | grep -i cache-control

# Lighthouse quick check (if available)
npx lighthouse https://mysite.com --output=json --quiet | jq '.categories | to_entries[] | "\(.key): \(.value.score * 100)%"'
```

### 4. Rollback Plan

Static sites make rollback trivial:

```bash
# Git-based: revert to previous commit
git revert HEAD && git push

# S3: restore previous version
aws s3 sync s3://my-bucket-backup s3://my-bucket --delete

# Cloudflare Pages: rollback to previous deployment in dashboard
# Or re-deploy the previous commit
git checkout HEAD~1 && npx wrangler pages deploy ./dist --project-name=my-site
```

## Agent Prompt Template

```
You are deploying a static site update.

1. Run pre-deploy validation (HTML check, broken links, missing assets)
2. If validation passes, deploy to [PLATFORM]
3. After deploy, verify the live site responds correctly
4. If any step fails, stop and report — do not force the deploy
5. Log the deployment: timestamp, what changed, deploy target, verification result
```

## Key Principles

- **Validate before deploy** — static sites are forgiving, but broken links and missing assets still hurt
- **Verify after deploy** — don't assume success; check the live URL
- **Keep rollback trivial** — git history is your safety net
- **Log deployments** — even for simple sites, a deploy log helps debug "when did this break?"
- **No build step needed** — for pure HTML/CSS sites, "deploy" is just file sync. Don't overcomplicate it.

## Anti-Patterns

- Deploying without checking if referenced assets exist locally
- Skipping post-deploy verification ("it worked last time")
- Using complex CI/CD for sites that are just HTML files
- Not invalidating CDN cache after deploy (stale content for hours)
