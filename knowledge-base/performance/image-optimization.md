# Image Optimization for Static Sites

_Last verified: 2026-03-23_

## Why This Matters

Images are the #1 cause of slow static sites. A single unoptimized hero image can add 3-5 seconds to page load. Google's Core Web Vitals (LCP, CLS) directly penalize this — and LCP is the metric most affected by images.

For Pagify sites, every image must be optimized before deployment. No exceptions.

---

## The Three Rules

1. **Right format** — use modern formats (WebP, AVIF) with fallbacks
2. **Right size** — serve the size the screen needs, not the original
3. **Right timing** — lazy-load everything below the fold

---

## Format Guide

| Format | Use When | Browser Support |
|---|---|---|
| **WebP** | Default for all photos and complex images | 97%+ (all modern browsers) |
| **AVIF** | When you need maximum compression (hero images, large photos) | ~92% (Chrome, Firefox, Safari 16.4+) |
| **SVG** | Logos, icons, simple illustrations | Universal |
| **PNG** | Only when transparency is required AND WebP isn't supported | Universal |
| **JPEG** | Legacy fallback only | Universal |

**Rule of thumb:** Convert everything to WebP. Use AVIF for hero images with a WebP fallback.

---

## Responsive Images with `<picture>`

Serve different sizes based on viewport width:

```html
<picture>
  <!-- AVIF for browsers that support it -->
  <source
    type="image/avif"
    srcset="/images/hero-400.avif 400w,
            /images/hero-800.avif 800w,
            /images/hero-1200.avif 1200w"
    sizes="100vw">
  <!-- WebP fallback -->
  <source
    type="image/webp"
    srcset="/images/hero-400.webp 400w,
            /images/hero-800.webp 800w,
            /images/hero-1200.webp 1200w"
    sizes="100vw">
  <!-- JPEG fallback for ancient browsers -->
  <img
    src="/images/hero-800.jpg"
    alt="Descriptive alt text"
    width="1200"
    height="630"
    loading="lazy"
    decoding="async">
</picture>
```

### Breakpoint Recommendations

For most Pagify sites (single-column or simple layouts):

| Breakpoint | Width | Use Case |
|---|---|---|
| Small | 400px | Mobile phones |
| Medium | 800px | Tablets, small laptops |
| Large | 1200px | Desktops |

Three sizes is enough for most sites. Don't over-engineer this.

---

## Lazy Loading

Add `loading="lazy"` to every image **except** the hero/above-the-fold image:

```html
<!-- Hero image: load immediately -->
<img src="/images/hero.webp" alt="..." width="1200" height="630" fetchpriority="high">

<!-- Below-fold images: lazy load -->
<img src="/images/team.webp" alt="..." width="800" height="450" loading="lazy" decoding="async">
```

**Why not lazy-load the hero?** The hero image is your Largest Contentful Paint (LCP) element. Lazy-loading it delays LCP and hurts your Core Web Vitals score. Use `fetchpriority="high"` instead.

---

## Always Set Width and Height

```html
<!-- ✅ Correct — prevents layout shift (CLS) -->
<img src="/images/photo.webp" alt="..." width="800" height="450">

<!-- ❌ Wrong — causes layout shift when image loads -->
<img src="/images/photo.webp" alt="...">
```

The browser uses `width` and `height` to reserve space before the image loads. Without them, the page jumps around as images appear — that's Cumulative Layout Shift (CLS), another Core Web Vitals metric.

CSS can still make images responsive:

```css
img {
  max-width: 100%;
  height: auto;
}
```

This scales images down on small screens while the HTML attributes prevent layout shift.

---

## Image Processing in Eleventy

Use the `@11ty/eleventy-img` plugin to automate format conversion and resizing at build time:

```js
// .eleventy.js
const Image = require("@11ty/eleventy-img");

async function imageShortcode(src, alt, sizes = "100vw") {
  let metadata = await Image(src, {
    widths: [400, 800, 1200],
    formats: ["avif", "webp", "jpeg"],
    outputDir: "./_site/images/",
    urlPath: "/images/"
  });

  return Image.generateHTML(metadata, {
    alt,
    sizes,
    loading: "lazy",
    decoding: "async"
  });
}

module.exports = function(eleventyConfig) {
  eleventyConfig.addAsyncShortcode("image", imageShortcode);
};
```

Then in templates:

```njk
{% image "src/images/hero.jpg", "A welcoming office lobby" %}
```

The plugin handles format conversion, resizing, and generating the correct `<picture>` markup. Source images go in as JPEG/PNG; optimized output gets generated at build time.

---

## Manual Optimization (No Build Tool)

If Eleventy image processing isn't set up, use these CLI tools before deployment:

### Convert to WebP

```bash
# Single file
cwebp -q 80 input.jpg -o output.webp

# Batch convert (all JPEGs in a directory)
for f in images/*.jpg; do cwebp -q 80 "$f" -o "${f%.jpg}.webp"; done
```

### Resize

```bash
# Using ImageMagick
magick input.jpg -resize 1200x -quality 80 output.jpg

# Using ffmpeg (already installed on most systems)
ffmpeg -i input.jpg -vf scale=1200:-1 output.jpg
```

### Quality Guidelines

| Image Type | WebP Quality | AVIF Quality | Notes |
|---|---|---|---|
| Hero/banner | 80-85 | 60-65 | Larger display, needs quality |
| Content photos | 75-80 | 55-60 | Good balance |
| Thumbnails | 70-75 | 50-55 | Small display, compress more |

---

## Core Web Vitals Targets

| Metric | Good | Needs Improvement | Poor |
|---|---|---|---|
| **LCP** (Largest Contentful Paint) | ≤ 2.5s | ≤ 4.0s | > 4.0s |
| **CLS** (Cumulative Layout Shift) | ≤ 0.1 | ≤ 0.25 | > 0.25 |
| **INP** (Interaction to Next Paint) | ≤ 200ms | ≤ 500ms | > 500ms |

For static sites, INP is rarely an issue (no heavy JS). Focus on LCP (image optimization) and CLS (width/height attributes).

---

## Pre-Deployment Checklist

Before deploying any Pagify site, verify:

- [ ] All images converted to WebP (AVIF for hero images)
- [ ] Responsive `srcset` provided for images wider than 400px
- [ ] `width` and `height` attributes on every `<img>`
- [ ] `loading="lazy"` on all below-fold images
- [ ] `fetchpriority="high"` on the hero/LCP image
- [ ] Alt text on every image (accessibility + SEO)
- [ ] No images wider than 1200px unless full-bleed design requires it
- [ ] Total page weight under 500KB for landing pages

---

## Testing Tools

- [PageSpeed Insights](https://pagespeed.web.dev/) — Google's official CWV test
- [WebPageTest](https://www.webpagetest.org/) — detailed waterfall analysis
- Chrome DevTools → Lighthouse → Performance audit

---

## For Coaches

When reviewing an agent-built site:
1. Open Chrome DevTools → Network tab → filter by "Img"
2. Check that images are WebP or AVIF (not raw JPEG/PNG)
3. Check file sizes — no single image should be over 200KB
4. Run PageSpeed Insights — LCP should be under 2.5s
5. Resize browser window — images should scale, not overflow

If images are unoptimized, the agent didn't finish the job.

---

## References

- [web.dev — Optimize images](https://web.dev/fast/#optimize-your-images)
- [web.dev — Core Web Vitals](https://web.dev/vitals/)
- [Eleventy Image Plugin](https://www.11ty.dev/docs/plugins/image/)
- [Can I Use — AVIF](https://caniuse.com/avif)
- [Can I Use — WebP](https://caniuse.com/webp)
