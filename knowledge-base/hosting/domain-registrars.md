# Domain Registrars — Evaluation for Coaches

_Last verified: 2026-03-17_

## Summary

Where to buy a domain name for a Pagify site. This matters because non-technical users are the most vulnerable to upsells, dark patterns, and hidden renewal pricing. Coaches need to know which registrars are safe to recommend.

**Bottom line:** Cloudflare Registrar or Porkbun. Both are honest on pricing, easy to use, and don't bury the client in upsell screens.

## The Coaching Problem

Domain registration is where most non-technical users get burned:

1. **Year-one discounts** — $0.99 first year, $18.99/year after. Client forgets, gets surprised.
2. **Upsells** — "Add privacy protection ($12/yr)! Add email ($5/mo)! Add SSL ($80/yr)!" Most of this is free elsewhere or unnecessary.
3. **Transfer locks** — Some registrars make it deliberately painful to leave.
4. **Auto-renewal traps** — Card on file gets charged; client didn't know.

A coach's job: get the client a domain at fair price, with no surprises.

## Recommended: Cloudflare Registrar

| Feature | Detail |
|---|---|
| **Pricing** | At-cost (wholesale ICANN price, no markup) |
| **.com price** | ~$10.11/year (2026) — same year 1, year 2, year 10 |
| **WHOIS privacy** | Free, automatic |
| **SSL** | Handled by Cloudflare Pages (free) |
| **DNS** | Cloudflare DNS (free, fast, reliable) |
| **Auto-renewal** | On by default; clear controls |
| **Account required** | Yes (same Cloudflare account as Pages) |
| **Transfer in** | Supported, at-cost pricing |
| **Transfer out** | No lock-in, standard EPP process |

### Why it fits Pagify

If the client is already on Cloudflare Pages (our default deployment), the domain stays in the same account. One login, one dashboard, one bill. DNS configuration is automatic — no manual CNAME records.

### Tradeoffs

- **No .dev, .app, or some newer TLDs** — Cloudflare's selection is growing but not exhaustive. Check availability first.
- **Requires Cloudflare DNS** — domains must use Cloudflare nameservers. Not a real limitation for Pagify sites, but worth noting.
- **No email hosting** — Cloudflare doesn't sell email. Use free email routing or a separate provider.

## Recommended Alternative: Porkbun

| Feature | Detail |
|---|---|
| **Pricing** | Low-cost, transparent renewal pricing shown at purchase |
| **.com price** | ~$10.33/year (2026) |
| **WHOIS privacy** | Free |
| **DNS** | Included, competent |
| **Interface** | Clean, minimal upsells |
| **Email** | Free email forwarding included |
| **Transfer** | Easy in and out |

### Why it's a good backup

Some clients already have a Porkbun account, or Cloudflare doesn't support their desired TLD. Porkbun is honest, cheap, and the UI doesn't try to confuse people.

## Acceptable: Namecheap

| Feature | Detail |
|---|---|
| **Pricing** | Competitive first year, slightly higher renewals |
| **.com price** | ~$9.98 first year, ~$15.98 renewal (2026) |
| **WHOIS privacy** | Free |
| **Interface** | Busier than Porkbun; some upsells but manageable |

Not first choice, but if a client already owns a domain there, don't force a transfer. Just point the DNS.

## Avoid for Pagify Clients

### GoDaddy
- Aggressive upselling at every step
- WHOIS privacy costs extra on some plans
- Renewal prices significantly higher than purchase
- Non-technical users get overwhelmed by the checkout flow
- **Coach rule:** If a client already has a GoDaddy domain, transfer it out during setup.

### Google Domains (Squarespace)
- Google Domains was shut down and transferred to Squarespace (2023-2024)
- Squarespace pricing is fair but the platform pushes toward their site builder
- If a client has a domain here, it's fine to keep — but new purchases go to Cloudflare/Porkbun

## Domain Pricing Reality Check

Coaches should set expectations:

| Item | Real cost |
|---|---|
| **.com domain** | $10-12/year at fair registrars |
| **WHOIS privacy** | $0 (should always be free) |
| **SSL certificate** | $0 (Cloudflare Pages provides this) |
| **DNS hosting** | $0 (included with registrar or Cloudflare) |
| **Email forwarding** | $0 (Cloudflare Email Routing or Porkbun) |

**Total real cost of owning a domain: ~$10-12/year.** That's it. If someone is paying more than $15/year for a basic .com with privacy, they're overpaying.

## Setup Flow for Coaches

### New domain (Cloudflare)

1. Client creates Cloudflare account (if they don't have one from Pages setup)
2. Go to Domain Registration → Register Domains
3. Search for desired domain
4. Purchase ($10-12 for .com, one-time annual charge)
5. Domain automatically uses Cloudflare DNS
6. In Cloudflare Pages → Custom Domains → add the domain
7. SSL activates automatically

Total time: 10-15 minutes. No DNS configuration needed.

### Existing domain (any registrar) → Cloudflare Pages

1. In Cloudflare → Add Site → enter domain → Free plan
2. Cloudflare scans existing DNS records
3. Update nameservers at current registrar to Cloudflare's (two NS records)
4. Wait for propagation (usually 15-60 minutes, can take up to 48h)
5. In Cloudflare Pages → Custom Domains → add the domain
6. CNAME record created automatically

**Coach tip:** Nameserver changes are the most confusing step for non-technical users. Do this together on a screen share, or provide exact screenshot-based instructions.

### Optional: Transfer domain to Cloudflare

Only worth doing if:
- Client is paying too much at current registrar
- Client wants everything in one place
- Current registrar is problematic (GoDaddy)

Transfer process:
1. Unlock domain at current registrar
2. Get EPP/authorization code
3. In Cloudflare → Transfer Domains → enter domain → enter auth code
4. Pay one year at Cloudflare's at-cost price
5. Transfer completes in 5-7 days

## Email: The Question That Always Comes Up

"I have a domain — can I get a matching email address?"

Options for Pagify clients:

| Solution | Cost | Best for |
|---|---|---|
| **Cloudflare Email Routing** | Free | Forward domain email to existing Gmail/Outlook |
| **Google Workspace** | $7.20/mo | Full business email (if client needs it) |
| **Zoho Mail Free** | Free (5 users) | Free business email with limitations |
| **Porkbun Email Forwarding** | Free | Simple forwarding if domain is at Porkbun |

**Coach recommendation:** Start with Cloudflare Email Routing. Client gets `hello@theirdomain.com` forwarding to their personal email. Free, takes 5 minutes, no new accounts needed. Upgrade to Google Workspace only if they need to *send from* the domain address regularly.

## References

- [Cloudflare Registrar](https://www.cloudflare.com/products/registrar/)
- [Cloudflare Email Routing](https://developers.cloudflare.com/email-routing/)
- [Porkbun](https://porkbun.com/)
- [ICANN Domain Pricing](https://www.icann.org/resources/pages/registrars/fees-en) (wholesale reference)
