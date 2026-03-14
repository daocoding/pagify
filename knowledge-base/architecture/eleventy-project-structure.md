# Eleventy Project Structure — Canonical Pattern

_Last verified: 2026-03-14_

## Summary

The standard file structure for a Pagify-built site using Eleventy. Every site follows this pattern. Coaches should internalize it — it's how every project starts.

## Directory Layout

```
my-site/
├── .eleventy.js          # Eleventy config (build settings)
├── package.json          # Node dependencies (just eleventy)
├── src/                  # All source content lives here
│   ├── _data/            # Global data files (site metadata, nav)
│   │   ├── site.json     # Site-wide metadata (title, url, description)
│   │   └── navigation.json  # Nav menu structure
│   ├── _includes/        # Reusable layout templates
│   │   ├── base.njk      # HTML skeleton (head, body, footer)
│   │   ├── page.njk      # Standard page layout (extends base)
│   │   └── post.njk      # Blog post layout (extends base)
│   ├── index.md          # Homepage
│   ├── about.md          # About page
│   ├── contact.md        # Contact page
│   ├── blog/             # Blog posts directory
│   │   ├── blog.json     # Directory-level defaults (layout, tags)
│   │   ├── first-post.md
│   │   └── second-post.md
│   ├── css/              # Stylesheets (copied as-is)
│   │   └── style.css
│   └── images/           # Images (copied as-is)
├── _site/                # Build output (git-ignored, deployed)
└── .gitignore
```

## Why This Structure

### `src/` as content root
Everything the site owner edits lives in `src/`. This separation matters:
- Coaches can say "everything you'll ever touch is in the `src` folder"
- Build config stays at root level, out of the way
- `_site/` is generated — never edit it directly

### `_data/` for site metadata
```json
// src/_data/site.json
{
  "title": "My Coaching Practice",
  "url": "https://mysite.com",
  "description": "Helping families build financial literacy",
  "language": "en"
}
```
One file, one place to update site-wide info. Templates reference `{{ site.title }}` etc.

### `_includes/` for layouts
Layouts use Nunjucks (`.njk`) — simple template language, easy for agents to generate:
```html
<!-- src/_includes/base.njk -->
<!DOCTYPE html>
<html lang="{{ site.language }}">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>{{ title }} — {{ site.title }}</title>
  <meta name="description" content="{{ description or site.description }}">
  <link rel="stylesheet" href="/css/style.css">
</head>
<body>
  <nav><!-- navigation from navigation.json --></nav>
  <main>{{ content | safe }}</main>
  <footer>© {{ site.title }}</footer>
</body>
</html>
```

### Content pages use front matter
```markdown
---
title: About Me
description: Learn about my coaching philosophy
layout: page.njk
---

I've been helping families for 15 years...
```

The front matter tells Eleventy which layout to use. The Markdown becomes the `{{ content }}` in the layout.

### Directory data files
```json
// src/blog/blog.json
{
  "layout": "post.njk",
  "tags": "post"
}
```
Every `.md` file in `blog/` automatically gets the `post` layout and `post` tag. No need to repeat front matter in every post.

## The `.eleventy.js` Config

```js
module.exports = function(eleventyConfig) {
  // Copy static assets as-is
  eleventyConfig.addPassthroughCopy("src/css");
  eleventyConfig.addPassthroughCopy("src/images");

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

Minimal. Tells Eleventy where things are and what to copy.

## The `package.json`

```json
{
  "name": "my-site",
  "private": true,
  "scripts": {
    "build": "npx @11ty/eleventy",
    "serve": "npx @11ty/eleventy --serve"
  },
  "devDependencies": {
    "@11ty/eleventy": "^3.0.0"
  }
}
```

One dependency. That's it.

## What Coaches Need to Know

1. **Content is Markdown** — non-technical users write in Markdown or plain text. Agents can generate it. Coaches can review it.
2. **Each page = one file = one URL** — `src/about.md` becomes `/about/`. No routing tables, no JavaScript needed.
3. **Layouts are templates** — change the layout once, every page updates. The site owner rarely touches these.
4. **Build is one command** — `npm run build` generates the entire site in `_site/`. That folder gets deployed.
5. **Local preview** — `npm run serve` starts a local server at `localhost:8080` with live reload.

## Anti-Patterns to Avoid

- **Don't put content at the root** — always use `src/` as input directory
- **Don't edit `_site/`** — it's regenerated on every build
- **Don't add JavaScript frameworks** — if the site needs React/Vue, Pagify is the wrong tool
- **Don't use complex Eleventy plugins** unless the coach understands them — keep the dependency surface small
- **Don't skip `site.json`** — hardcoding site title in templates makes maintenance painful

## References

- [Eleventy Getting Started](https://www.11ty.dev/docs/getting-started/)
- [Eleventy Directory Structure](https://www.11ty.dev/docs/config/#input-directory)
- [Nunjucks Templating](https://mozilla.github.io/nunjucks/)
- [Eleventy Data Files](https://www.11ty.dev/docs/data-global/)
