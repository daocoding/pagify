# Agent Pattern: Content Update on an Existing Site

_Last verified: 2026-03-16_

## Summary

How an AI agent safely adds or modifies content on a live Pagify site. This covers blog posts, page edits, new pages, and content corrections — the bread-and-butter of ongoing site maintenance.

## When This Pattern Applies

- Coach says "add a blog post about our March workshop"
- Client wants to update their services page
- Agent detects stale content during a periodic review
- New testimonial or event needs to be published

## Step 1: Pull and Verify Current State

```bash
git pull origin main
npm run build
```

**Never skip this.** The agent must confirm the site builds cleanly before making changes. If it doesn't build, fix that first — don't pile changes on a broken foundation.

## Step 2: Determine Change Type

| Change Type | What to Do |
|---|---|
| **New blog post** | Create `src/blog/YYYY-MM-DD-slug.md` with front matter |
| **Edit existing page** | Modify the `.md` file directly |
| **New standalone page** | Create `src/page-name.md`, add to `navigation.json` if needed |
| **Delete a page** | Remove the `.md` file, remove from `navigation.json`, check for internal links |

## Step 3: Write the Content

### For blog posts:

```markdown
---
title: March Community Workshop Recap
description: Highlights from our March 2026 financial literacy workshop in Raleigh
date: 2026-03-15
layout: post.njk
tags: post
---

## What Happened

[Real content here — never placeholder text]
```

### For page edits:

- Make the specific change requested
- Don't rewrite surrounding content unless asked
- Preserve existing front matter fields
- Update `description` if the page topic changed materially

### Content rules (always apply):

1. **Never generate fake testimonials or events.** If the coach says "add a testimonial," it must come from a real person with their permission.
2. **Dates must be real.** Don't backdate posts or fabricate event dates.
3. **Maintain voice consistency.** Read 2-3 existing pages before writing new content. Match the tone.
4. **Every new page needs a unique meta description.** No duplicates.

## Step 4: Update Structured Data (if applicable)

If the change involves:
- **New service** → update `ProfessionalService` schema `serviceType` array
- **New FAQ** → add to `FAQPage` schema
- **New blog post** → `Article` schema is handled by the post layout automatically
- **Address/contact change** → update `LocalBusiness` schema

## Step 5: Update Navigation (if applicable)

If adding a new standalone page that should be in the nav:

```json
// src/_data/navigation.json — add the new entry
{ "label": "New Page", "url": "/new-page/" }
```

**Don't add every blog post to the nav.** Blog posts are discovered through the blog index, not the nav bar.

## Step 6: Build and Verify

```bash
npm run build
npm run serve
```

### Verification checklist:

- [ ] Site builds without errors
- [ ] New/modified page renders correctly
- [ ] Page has unique title and meta description
- [ ] All internal links still work (no new 404s)
- [ ] If blog post: appears on blog index page
- [ ] If nav change: navigation renders correctly on all pages
- [ ] If structured data change: validates in [Rich Results Test](https://search.google.com/test/rich-results)

## Step 7: Commit and Push

```bash
git add .
git commit -m "Add: March workshop recap blog post"
git push origin main
```

### Commit message conventions:

| Prefix | When |
|---|---|
| `Add:` | New content (page, post, image) |
| `Update:` | Modified existing content |
| `Fix:` | Corrected an error (broken link, typo, wrong date) |
| `Remove:` | Deleted content |

## Periodic Review Pattern

Agents configured for ongoing maintenance should check monthly:

1. **Stale content** — any page referencing past events as "upcoming"?
2. **Broken external links** — do all outbound links still resolve?
3. **Contact info accuracy** — phone, email, hours still correct?
4. **Structured data validity** — re-run Rich Results Test on key pages
5. **New content opportunities** — has the coach mentioned recent events or achievements that deserve a post?

Report findings to the coach before making changes. Don't silently update content that might be intentionally left as-is.

## Common Mistakes

1. **Editing `_site/` instead of `src/`** — `_site/` is regenerated on build. Changes there are lost.
2. **Forgetting to pull before editing** — leads to merge conflicts and lost work.
3. **Changing layout templates without testing all pages** — a template change affects every page using that layout.
4. **Adding a page without a description** — the template falls back to the site-wide description, which means duplicate meta descriptions across pages.
5. **Committing `node_modules/` or `_site/`** — both are in `.gitignore` for a reason.

## For Coaches

When an agent says "I've updated the site," verify:

1. Visit the live URL — does the change appear?
2. Is the new content accurate (dates, names, facts)?
3. Does the page look right on mobile?
4. If it's a new page, can you find it through the navigation?

If something's wrong, tell the agent specifically what to fix. "It looks weird" is harder to act on than "the date says March when it should be April."

## References

- [Scaffold New Site Pattern](/knowledge-base/agent-patterns/scaffold-new-site.md)
- [Eleventy Project Structure](/knowledge-base/architecture/eleventy-project-structure.md)
- [SEO Meta Tags Checklist](/knowledge-base/seo/meta-tags-checklist.md)
