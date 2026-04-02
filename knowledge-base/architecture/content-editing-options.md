# Content Editing Options for Static Sites

> How non-technical site owners update their content after launch — without breaking anything.

_Last verified: 2026-04-02_

---

## The Problem

Static sites are fast, secure, and cheap to host. But editing them typically means opening a code editor, writing Markdown or HTML, committing to Git, and triggering a deploy. That's fine for developers. It's a non-starter for the people Pagify serves.

The gap between "your site is live" and "you can update it yourself" is where most coaching projects fail.

---

## Options Evaluated

### 1. Git-Based CMS (Recommended Starting Point)

These provide a visual editing UI that commits changes to the same Git repo. No database. No extra hosting.

| Service | Free Tier | Strengths | Weaknesses |
|---|---|---|---|
| **Decap CMS** (formerly Netlify CMS) | ✅ Fully free, self-hosted | Open source, works with any Git host, simple config | UI is functional but plain; no real-time preview |
| **Sveltia CMS** | ✅ Fully free, self-hosted | Drop-in Decap replacement with better UI, i18n support, asset management | Newer project, smaller community |
| **Tina CMS** | ✅ Free for 2 users | Visual editing with live preview, Git-backed | Free tier limited; heavier setup; React dependency |
| **Keystatic** | ✅ Free, self-hosted | TypeScript-native, excellent DX, GitHub/local modes | Requires Astro or Next.js (not Eleventy-native) |

**Recommendation for Pagify coaches:** Start with **Decap CMS** or **Sveltia CMS**. Both:
- Add a single `admin/` folder to the Eleventy project
- Configure via one `config.yml` file
- Let clients log in at `yoursite.com/admin` and edit through a form-based UI
- Commit changes directly to the Git repo, triggering automatic redeploy

### 2. Headless CMS (For Growing Sites)

These store content externally and deliver it via API. More powerful, more moving parts.

| Service | Free Tier | Best For | Watch Out |
|---|---|---|---|
| **Sanity** | ✅ 3 users, 500K API calls/mo | Structured content, multi-site, real-time collaboration | Requires build step to pull content; learning curve |
| **Strapi** | ✅ Self-hosted (free forever) | Full control, custom content types, REST + GraphQL | You host it — needs a server or container |
| **Contentful** | ✅ 1 space, 5 users | Enterprise-grade, excellent API, webhooks | Free tier is tight; vendor lock-in risk |
| **Hygraph** (formerly GraphCMS) | ✅ 3 users, 2 locales | GraphQL-native, good for multilingual | Smaller ecosystem |

**When to move here:** When the site owner needs to manage many pages, multiple content types (blog + events + team bios), or when multiple people edit content.

### 3. File-Based Editing (No CMS)

For the simplest sites, skip the CMS entirely:

- **GitHub.com web editor** — Edit Markdown files directly in the browser. Free. No setup. Works if the person is comfortable with GitHub's UI (many aren't).
- **Prose.io** — A cleaner editing UI for GitHub-hosted content. Free, open source. Aging project but still functional.
- **Stackbit** — Visual page builder that works with Git repos. Was excellent; acquired by Netlify in 2024 — future uncertain.

**When this works:** Single-page or 2–3 page sites where updates happen quarterly, not weekly.

### 4. Hybrid: Agent-Assisted Editing

This is where Pagify's model shines. Instead of training the site owner to use a CMS:

1. Site owner describes the change (email, chat, voice memo)
2. A Claw/agent makes the edit, commits, and deploys
3. Site owner reviews the live result

**Tradeoffs:**
- ✅ Zero learning curve for the site owner
- ✅ Agent can handle structural changes, not just content swaps
- ⚠️ Creates dependency on the agent/coach (the thing Pagify is trying to avoid)
- ⚠️ Not free — agent usage has costs

**Best as a bridge:** Use agent-assisted editing for the first month while the site owner gets comfortable, then transition to a Git-based CMS for routine updates.

---

## Integration with Eleventy

### Decap CMS Setup (Minimal)

```
your-site/
├── admin/
│   ├── index.html    ← CMS entry point
│   └── config.yml    ← Content model + Git settings
├── src/
│   ├── posts/        ← Markdown files the CMS edits
│   └── pages/
└── .eleventy.js
```

**`admin/index.html`:**
```html
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Content Manager</title>
  <script src="https://unpkg.com/decap-cms@^3.0.0/dist/decap-cms.js"></script>
</head>
<body></body>
</html>
```

**`admin/config.yml`:**
```yaml
backend:
  name: github
  repo: owner/repo-name
  branch: main

media_folder: "src/images/uploads"
public_folder: "/images/uploads"

collections:
  - name: "posts"
    label: "Blog Posts"
    folder: "src/posts"
    create: true
    slug: "{{year}}-{{month}}-{{day}}-{{slug}}"
    fields:
      - { label: "Title", name: "title", widget: "string" }
      - { label: "Date", name: "date", widget: "datetime" }
      - { label: "Body", name: "body", widget: "markdown" }
```

That's it. Deploy, visit `/admin`, authenticate with GitHub, and the client can create and edit blog posts through a form UI.

---

## Decision Matrix for Coaches

| Situation | Recommendation |
|---|---|
| Client will rarely update content | File-based or agent-assisted |
| Client wants to blog regularly | Decap/Sveltia CMS |
| Multiple editors, structured content | Headless CMS (Sanity or Strapi) |
| Client is intimidated by any UI | Agent-assisted → transition to CMS later |
| Coach wants simplest possible setup | Decap CMS (one folder, one config file) |

---

## What to Tell the Client

> "Your website is yours. You can update it anytime by logging in at yoursite.com/admin. Type your changes, hit publish, and the site updates in about 60 seconds. If you ever get stuck, [coach name] can help — or the AI assistant can make the change for you."

The goal is **confidence**, not expertise. They don't need to understand Git, builds, or deploys. They need to know: I can change my own website, and it won't break.

---

## References

- [Decap CMS docs](https://decapcms.org/docs/)
- [Sveltia CMS](https://github.com/sveltia/sveltia-cms)
- [Tina CMS](https://tina.io/docs/)
- [Sanity](https://www.sanity.io/docs)
- [Eleventy + Decap tutorial](https://www.11ty.dev/docs/cms/)
