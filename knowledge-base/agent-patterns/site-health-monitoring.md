# Site Health Monitoring for Claws

How an AI agent (Claw) monitors and maintains the health of a deployed static site.

_Last verified: 2026-03-26_

---

## Why This Matters

A site that goes live and never gets checked is a site that silently breaks. SSL certificates expire, links rot, hosting free tiers change terms, and content goes stale. Non-technical site owners won't notice until someone tells them — or until they lose trust in the site entirely.

A Claw running periodic health checks catches these problems before the site owner even knows they exist. That's the difference between "I built you a site" and "your site is taken care of."

---

## What to Monitor

### 1. Uptime & HTTP Status

The most basic check. Is the site responding?

```bash
# Check homepage returns 200
curl -s -o /dev/null -w '%{http_code}' https://example.com/

# Check key pages (not just homepage)
for page in / /about /contact /services; do
  status=$(curl -s -o /dev/null -w '%{http_code}' "https://example.com${page}")
  echo "${page}: ${status}"
done
```

**What to look for:**
- `200` — healthy
- `301`/`302` — redirect; verify it goes where intended
- `404` — page missing; was it removed or is the URL wrong?
- `5xx` — hosting issue; check Cloudflare/platform status
- Connection timeout — DNS issue, hosting down, or domain expired

**Frequency:** Daily at minimum. More if the site is business-critical.

### 2. SSL Certificate Expiry

Free certificates (Let's Encrypt, Cloudflare) auto-renew, but auto-renewal can fail silently.

```bash
# Check SSL expiry date
echo | openssl s_client -servername example.com -connect example.com:443 2>/dev/null \
  | openssl x509 -noout -enddate
# Output: notAfter=Jun 15 00:00:00 2026 GMT
```

**Alert threshold:** 14 days before expiry. If auto-renewal failed, the site owner needs to know before browsers start showing warnings.

### 3. Broken Links

Links rot. External sites go down, pages get reorganized, typos sneak in.

```bash
# Using wget spider mode (available on most systems)
wget --spider --recursive --level=2 --no-verbose \
  --output-file=/tmp/link-check.log https://example.com/ 2>&1

# Check for broken links in the log
grep -i "broken" /tmp/link-check.log
```

For agents with Node.js available:
```bash
npx broken-link-checker https://example.com/ --ordered --recursive
```

**Frequency:** Weekly. External links break unpredictably.

**What to do when found:**
- Internal broken link → fix the link, commit, redeploy
- External broken link → try Internet Archive Wayback Machine for replacement, or remove the link
- Report to site owner with specifics: which page, which link, what it pointed to

### 4. Content Freshness

Static sites can go stale. A "2024 pricing" page in 2026 erodes trust.

**Check for:**
- Copyright year in footer (should be current or use a range)
- Dates in content that have passed ("upcoming event on March 2025")
- "Last updated" timestamps older than the owner's stated cadence
- Seasonal content that's out of season

```bash
# Simple grep for outdated years in built HTML
grep -rn "202[0-4]" _site/ --include="*.html" | grep -v node_modules
```

**Frequency:** Monthly.

### 5. Performance (Core Web Vitals)

Google uses Core Web Vitals for ranking. A slow site is an invisible site.

```bash
# Using Google PageSpeed Insights API (free, no key needed for basic)
curl -s "https://www.googleapis.com/pagespeedonline/v5/runPagespeed?url=https://example.com/&strategy=mobile" \
  | python3 -c "import sys,json; d=json.load(sys.stdin); print(json.dumps(d.get('lighthouseResult',{}).get('categories',{}).get('performance',{}), indent=2))"
```

**Thresholds:**
- Performance score > 90: good
- 50-90: needs attention
- < 50: urgent — likely image optimization or hosting issue

**Frequency:** Monthly, or after any content/structure change.

### 6. DNS & Domain Health

Domain expiry is catastrophic and entirely preventable.

```bash
# Check domain expiry (requires whois)
whois example.com | grep -i "expir"

# Verify DNS records point where expected
dig +short example.com A
dig +short example.com CNAME
dig +short www.example.com CNAME
```

**Alert threshold:** 30 days before domain expiry.

---

## Monitoring Workflow for a Claw

### Daily (lightweight, <30 seconds)
1. HTTP status check on homepage + 2-3 key pages
2. Log result to monitoring file

### Weekly
1. Full broken link scan
2. SSL certificate expiry check
3. Log any issues found

### Monthly
1. Content freshness audit
2. Performance score check
3. DNS/domain expiry check
4. Summary report to site owner (only if issues found)

### On Every Deploy
1. Verify all pages return 200
2. Check that new content renders correctly (spot-check URLs)
3. Confirm SSL still valid post-deploy

---

## Reporting to Site Owners

Non-technical people don't want raw logs. They want:

**✅ "Your site is healthy."** — One sentence, once a month, if everything is fine. Don't over-report.

**⚠️ "I found something that needs attention."** — Plain language. What's wrong, why it matters, what you'll do about it (or what they need to do).

Example:
> Your site has 2 broken links on the Services page. Both point to an external site that went offline. I've removed them and updated the page. Here's what changed: [link to commit].

**🚨 "This needs immediate action."** — SSL expiring in 3 days, domain expiring in 2 weeks, site returning errors. Be direct, be specific, include the fix.

---

## What NOT to Monitor

- **Analytics/traffic** — that's the site owner's business, not a health issue
- **Competitor sites** — out of scope for maintenance
- **SEO rankings** — fluctuations are normal; don't create noise

Keep monitoring focused on **"is the site working correctly?"** — not "is the site successful?"

---

## Tools Summary

| Tool | Purpose | Cost |
|---|---|---|
| `curl` | HTTP status, basic checks | Free (pre-installed) |
| `openssl` | SSL certificate checks | Free (pre-installed) |
| `wget --spider` | Broken link detection | Free (pre-installed) |
| `whois` | Domain expiry | Free (pre-installed) |
| Google PageSpeed API | Performance scoring | Free |
| `broken-link-checker` (npm) | Thorough link checking | Free |

No paid services needed. Everything runs from the command line.
