# Eleventy Common Errors & Fixes

_Last verified: 2026-03-31_

When Eleventy builds break, the error messages can be cryptic — especially for coaches who aren't full-time developers. This guide covers the errors you'll actually hit, what they mean, and how to fix them.

## Build Won't Start

### `Error: Cannot find module '@11ty/eleventy'`

**What happened:** Eleventy isn't installed in this project.

**Fix:**
```bash
npm install --save-dev @11ty/eleventy
```

**Why:** Node.js packages are per-project. Even if Eleventy works in another folder, each site needs its own install.

---

### `Error: ENOENT: no such file or directory, open 'src/_includes/base.njk'`

**What happened:** A template references a layout file that doesn't exist (or the path is wrong).

**Fix:** Check the `layout` field in your Markdown front matter:
```yaml
---
layout: base.njk      # Must match the actual filename in src/_includes/
---
```

Common mistakes:
- Typo in filename (`Base.njk` vs `base.njk` — case matters on Linux/Mac)
- Missing `_includes` directory
- Wrong `dir.includes` path in `.eleventy.js`

---

### `(node:12345) Warning: To load an ES module, set "type": "module" in package.json`

**What happened:** You're using Eleventy 3.x with an older config file format.

**Fix for Eleventy 2.x (recommended for Pagify):** Keep using `module.exports` in `.eleventy.js`. No changes needed.

**Fix for Eleventy 3.x:** Rename `.eleventy.js` to `eleventy.config.js` and use:
```js
export default function(eleventyConfig) {
  // ... config
};
```

**Pagify recommendation:** Stick with Eleventy 2.x (`@11ty/eleventy@^2.0.0`) for stability. The 3.x ESM migration adds complexity with no benefit for static content sites.

---

## Template Errors

### `Template render error: attempted to output null or undefined value`

**What happened:** A Nunjucks template uses a variable that doesn't exist.

**Fix:** Find which variable is null. Common culprits:

```html
<!-- This fails if site.json is missing or has no "title" key -->
<title>{{ title }} — {{ site.title }}</title>
```

Check:
1. Does `src/_data/site.json` exist and is it valid JSON?
2. Does the page's front matter include a `title` field?
3. Run `npx @11ty/eleventy --dryrun` to see which file triggers the error.

**Prevention:** Always set defaults in templates:
```html
<title>{{ title or "Untitled" }} — {{ site.title or "My Site" }}</title>
```

---

### `Template render error: expected variable end`

**What happened:** Nunjucks syntax error — usually a missing `}}` or `%}`.

**Fix:** Look for unmatched brackets in the file mentioned in the error:
```html
<!-- Wrong -->
<p>{{ site.title </p>

<!-- Right -->
<p>{{ site.title }}</p>
```

**Tip:** Nunjucks uses `{{ }}` for output, `{% %}` for logic, and `{# #}` for comments. Mixing these up causes this error.

---

### `TemplateLayoutPathResolverError: Could not find layout file`

**What happened:** The layout chain is broken. A layout extends another layout that doesn't exist.

**Fix:** Trace the chain:
1. Your page says `layout: page.njk`
2. `page.njk` says `layout: base.njk`
3. Does `base.njk` exist in `src/_includes/`?

Check your `.eleventy.js` for the includes directory setting:
```js
return {
  dir: {
    includes: "_includes"  // Layouts live here
  }
};
```

---

## Data Errors

### `SyntaxError: Unexpected token } in JSON at position ...`

**What happened:** A JSON data file (`src/_data/*.json`) has invalid syntax.

**Fix:** Common JSON mistakes:
```json
// Wrong — trailing comma
{
  "title": "My Site",
  "url": "https://example.com",
}

// Right
{
  "title": "My Site",
  "url": "https://example.com"
}
```

**Quick validation:** Paste your JSON into any online validator, or run:
```bash
node -e "require('./src/_data/site.json')"
```

---

### Data file exists but variables are empty in templates

**What happened:** The data file is in the wrong location or has the wrong name.

**How Eleventy data files work:**
- `src/_data/site.json` → available as `{{ site.title }}` in all templates
- `src/_data/navigation.json` → available as `{{ navigation }}` (array)
- The filename becomes the variable name (without extension)

**Check:**
1. Is the file inside `src/_data/` (not `_data/` at the root)?
2. Is the JSON valid?
3. Does the variable name in the template match the filename?

---

## Deployment Errors

### Cloudflare Pages: `Error: Build failed with exit code 1`

**What happened:** The build command or Node version is wrong.

**Fix in Cloudflare Pages dashboard:**
- Build command: `npx @11ty/eleventy`
- Output directory: `_site`
- Environment variable: `NODE_VERSION` = `18`

**Common cause:** Cloudflare defaults to an old Node.js version that doesn't support Eleventy 2.x. Always set `NODE_VERSION`.

---

### Cloudflare Pages: Site deploys but shows 404

**What happened:** The output directory setting doesn't match where Eleventy puts files.

**Check:** Your `.eleventy.js` output setting must match your Cloudflare config:
```js
return {
  dir: {
    output: "_site"  // Cloudflare must be set to "_site" too
  }
};
```

---

### Pages deploy but links are broken

**What happened:** Relative paths work locally but break on the deployed URL.

**Fix:** Always use root-relative paths in navigation and links:
```html
<!-- Wrong — breaks in subdirectories -->
<a href="about/">About</a>

<!-- Right — works everywhere -->
<a href="/about/">About</a>
```

**Also check:** Eleventy generates directory-style URLs by default (`/about/index.html` → `/about/`). If you link to `/about` without the trailing slash, some hosts redirect (adding latency) or 404.

---

## Performance Issues

### Build takes more than 10 seconds for a small site

**What happened:** Usually an issue with passthrough copy or an accidentally large directory.

**Check:**
1. Is `node_modules/` being copied? (It shouldn't be — check `.eleventy.js` passthrough config)
2. Are there large images in `src/images/`? (Optimize them — see [Image Optimization guide](/knowledge-base/performance/image-optimization.md))
3. Is a watch process copying files in a loop? (Kill existing `npm run serve` processes before starting a new one)

---

## The Debug Checklist

When something breaks and you're not sure what, run through these in order:

1. **Is `node_modules/` present?** If not: `npm install`
2. **Is `.eleventy.js` valid?** Run `node -c .eleventy.js`
3. **Are data files valid JSON?** Run `node -e "require('./src/_data/site.json')"`
4. **Does a dry run succeed?** Run `npx @11ty/eleventy --dryrun`
5. **What's the actual error?** Run `npx @11ty/eleventy 2>&1 | head -20`
6. **Is it a template issue?** Simplify the failing template to just `<p>hello</p>` and rebuild

If you've gone through all six and it's still broken, open a [GitHub Discussion](https://github.com/daocoding/pagify/discussions) with:
- The exact error message
- Your Node.js version (`node --version`)
- Your Eleventy version (`npx @11ty/eleventy --version`)
- The relevant file contents

---

## For Agents

When debugging a build failure:

1. **Read the full error first** — don't guess from the first line
2. **Run `--dryrun`** — it catches template errors without writing files
3. **Fix one thing at a time** — multiple simultaneous changes make debugging harder
4. **Report what you fixed** — the coach needs to understand what went wrong so they can catch it next time

Never silently fix an error and move on. The coach learning from the error is more valuable than the fix itself.
