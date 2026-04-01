# Client Intake Guide for Coaches

How to run a structured discovery session with a non-technical person before any code is written.

---

## Why This Matters

The #1 reason workshop attendees never ship: they leave with inspiration but no clarity. A 30-minute intake session converts vague desire into a buildable spec.

This guide is for **coaches** — the human in the loop between a non-technical person and the AI agent that builds their site.

---

## The Five Questions

Every intake boils down to five things. Get these right and the agent can scaffold immediately.

### 1. What is this for?

Not "what kind of site do you want?" — that gets you "a nice one."

Ask instead:
- "If someone lands on your site, what should they **do**?"
- "What's the one thing you want people to walk away knowing?"

**Map the answer to a site type:**

| Answer pattern | Site type | Pages needed |
|---|---|---|
| "I want people to contact me" | Lead generation | Home, About, Services, Contact |
| "I want to share what I know" | Content/blog | Home, About, Blog, Contact |
| "I want to sell something" | Product showcase | Home, Products, About, Contact |
| "I want people to find my business" | Local presence | Home, Services, Location/Hours, Contact |
| "I want a place for my community" | Hub/portal | Home, Resources, Events, About, Contact |

Most non-technical people need 4–6 pages. Resist scope creep.

### 2. Who is it for?

- "Describe one specific person who would visit your site."
- "How would they find you — Google, word of mouth, social media?"
- "What language(s) do they speak?"

This determines: copy tone, SEO keywords, whether you need i18n (see `../i18n/multilingual-static-sites.md`), and structured data type.

### 3. What do you already have?

Inventory existing assets before creating anything:

- **Text:** Bio, business description, service list, testimonials, social media posts they're proud of
- **Images:** Logo, headshot, product photos, event photos (check quality — blurry phone photos need replacement)
- **Accounts:** Domain name? Google Business Profile? Social media handles?
- **Branding:** Colors they use? Fonts they like? Sites they admire?

**Coach tip:** Most people underestimate what they already have. Ask to see their social media — there's usually 6 months of content sitting there.

### 4. What's the budget for ongoing costs?

Be direct. Many non-technical people assume websites cost $50/month.

| Item | Free option | Paid option |
|---|---|---|
| Hosting | Cloudflare Pages (see `../hosting/cloudflare-pages.md`) | — |
| Domain | `*.pages.dev` subdomain | ~$10–15/year (see `../hosting/domain-registrars.md`) |
| Email | Gmail / existing provider | Google Workspace ~$7/mo |
| Analytics | Cloudflare Web Analytics | — |
| SSL | Included with Cloudflare | — |

**Realistic minimum:** $0/year (free subdomain + Cloudflare Pages). **Recommended minimum:** ~$12/year (custom domain only).

### 5. Who maintains it after launch?

This is the most important question. It determines the ownership path.

Present the three options plainly:

1. **"You do it yourself"** — We build it, teach you how to edit files, you own everything. Best if: you're willing to learn basic text editing, you have a computer you use regularly.

2. **"Your own AI agent"** — We set up an OpenClaw agent that maintains the site for you. You talk to it in plain language. Best if: you're comfortable with a chat interface, you want updates without learning code.

3. **"A volunteer helps you"** — A community member's agent handles updates for you. Best if: you want minimal involvement, you're okay with someone else having access.

**Coach tip:** Don't push option 2 or 3 just because they're easier. Some people genuinely want to learn. That's the whole point.

---

## Intake Session Template

Use this as a worksheet. Fill it in during the conversation.

```markdown
# Site Intake — [Client Name]
Date: YYYY-MM-DD
Coach: [Your name]

## Purpose
- Primary action visitors should take:
- One-sentence site description:
- Site type: [lead-gen / content / product / local / hub]

## Audience
- Primary visitor profile:
- How they find the site:
- Language(s):

## Existing Assets
- [ ] Bio / about text
- [ ] Business description
- [ ] Service or product list
- [ ] Testimonials
- [ ] Logo
- [ ] Headshot
- [ ] Other photos
- [ ] Domain name: _______________
- [ ] Social media handles: _______________
- [ ] Brand colors: _______________
- [ ] Sites they admire: _______________

## Pages Needed
1.
2.
3.
4.
5.

## Budget
- Domain: [free subdomain / custom domain]
- Hosting: Cloudflare Pages (free)
- Other: _______________

## Maintenance Path
- [ ] Self-sovereign
- [ ] Own agent
- [ ] Volunteer agent

## Notes
```

---

## After the Intake

1. **Create a project folder** with the client's name
2. **Copy the filled worksheet** into the folder as `intake.md`
3. **Collect assets** — ask the client to send everything to one place (email, shared folder)
4. **Hand off to the agent** — the intake worksheet is the agent's brief (see `../agent-patterns/scaffold-new-site.md`)

The agent reads the intake, scaffolds the site structure, and generates initial content. The coach reviews before the client ever sees it.

---

## Common Pitfalls

**"I want it to look like Apple's website."**
Redirect: "What specifically do you like about it? The clean layout? The big images? The simplicity?" Extract the principle, not the reference.

**"Can we add a store / booking system / member login?"**
Not in v1. Get the static site live first. Dynamic features are phase 2 — and often unnecessary once the person sees what a well-built static site can do.

**"I don't know what I want."**
That's fine. Start with: "Tell me about your work." Listen for 5 minutes. The site will reveal itself.

**"My nephew already started building something."**
Ask to see it. If it's a WordPress site with 47 plugins, a Wix site on a paid plan, or an SPA that doesn't show up in search — explain the tradeoffs honestly. Don't trash their nephew's work. Focus on what Pagify does differently (ownership, simplicity, findability).

---

## Related

- [Scaffold New Site (Agent Pattern)](../agent-patterns/scaffold-new-site.md)
- [Cloudflare Pages Setup](../hosting/cloudflare-pages.md)
- [SEO Meta Tags Checklist](../seo/meta-tags-checklist.md)
- [Multilingual Sites](../i18n/multilingual-static-sites.md)
