# Web Accessibility Essentials for Pagify Sites

_Last verified: 2026-03-21_

## Summary

Accessibility isn't optional — it's how you make sure everyone can use your site, including people with visual, motor, cognitive, or hearing differences. For coaches serving diverse communities, this is both a legal requirement (ADA, Section 508) and a moral one.

Pagify's static HTML architecture is already a head start. No JavaScript-dependent rendering means screen readers can parse your content immediately. But architecture alone isn't enough — you still need to get the details right.

## Why Coaches Should Care

Your potential clients include:

- **Older adults** planning retirement — often with reduced vision or motor dexterity
- **People using screen readers** — approximately 7 million Americans with significant vision loss
- **Mobile users in poor conditions** — low bandwidth, bright sunlight, one-handed use
- **Non-native speakers** — clear structure helps comprehension regardless of language ability

An inaccessible site silently excludes exactly the people community coaches are trying to reach.

## WCAG 2.2 — What Matters Most

The Web Content Accessibility Guidelines (WCAG) have three levels: A, AA, AAA. **Target AA** — it's the legal standard and covers the most impactful requirements.

### 1. Color Contrast

Text must have sufficient contrast against its background:

- **Normal text** (under 18px): minimum contrast ratio **4.5:1**
- **Large text** (18px+ or 14px+ bold): minimum contrast ratio **3:1**
- **UI components** (buttons, form borders): minimum **3:1**

```css
/* ✅ Good — dark text on light background */
body {
  color: #333333;          /* contrast ratio ~12:1 on white */
  background-color: #ffffff;
}

/* ❌ Bad — light gray on white */
body {
  color: #aaaaaa;          /* contrast ratio ~2.3:1 on white */
  background-color: #ffffff;
}
```

**Tool:** [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/) — paste your hex colors, get instant pass/fail.

### 2. Semantic HTML

Use the right elements for the right purpose. This is where Pagify's Eleventy templates shine — get it right in the template, every page inherits it.

```html
<!-- ✅ Correct structure -->
<header>
  <nav aria-label="Main navigation">
    <ul>
      <li><a href="/">Home</a></li>
      <li><a href="/about/">About</a></li>
      <li><a href="/services/">Services</a></li>
      <li><a href="/contact/">Contact</a></li>
    </ul>
  </nav>
</header>

<main>
  <h1>Financial Coaching for Families</h1>
  <section>
    <h2>What We Offer</h2>
    <!-- content -->
  </section>
</main>

<footer>
  <p>&copy; 2026 Jane Smith Financial Coaching</p>
</footer>
```

**Key rules:**

- One `<h1>` per page — it's the page title
- Headings in order — don't skip from `<h2>` to `<h4>`
- Use `<nav>` for navigation, `<main>` for primary content, `<footer>` for footer
- Use `<button>` for actions, `<a>` for links — never `<div onclick>`
- Use `<ul>` / `<ol>` for lists — screen readers announce the count

### 3. Image Alt Text

Every `<img>` needs an `alt` attribute. What goes in it depends on the image's purpose:

```html
<!-- Informative image — describe what it shows -->
<img src="team-workshop.jpg" 
     alt="Coach Jane leading a financial literacy workshop with six participants around a table">

<!-- Decorative image — empty alt (screen reader skips it) -->
<img src="decorative-divider.png" alt="">

<!-- Image that IS the link/button — describe the action -->
<a href="/contact/">
  <img src="email-icon.png" alt="Contact us">
</a>
```

**Rules for coaches reviewing agent-generated sites:**

- If `alt=""` is missing entirely → broken. Screen readers will read the filename.
- If every image says `alt="image"` → the agent was lazy. Each alt should be unique and descriptive.
- If a photo has text in it → the alt text must include that text.

### 4. Keyboard Navigation

Everything clickable must be reachable by keyboard (Tab key) and activatable by Enter/Space:

```css
/* ✅ Visible focus indicator — don't remove this */
:focus {
  outline: 3px solid #2563eb;
  outline-offset: 2px;
}

/* ❌ Never do this */
:focus {
  outline: none;  /* kills keyboard navigation visibility */
}
```

**Test it yourself:** Put your mouse away. Navigate your entire site using only Tab, Shift+Tab, Enter, and Escape. If you can't reach something, it's broken.

### 5. Form Accessibility

Contact forms are the most common interactive element on Pagify sites:

```html
<form action="/contact/" method="POST">
  <div>
    <label for="name">Your name</label>
    <input type="text" id="name" name="name" required 
           autocomplete="name">
  </div>
  
  <div>
    <label for="email">Email address</label>
    <input type="email" id="email" name="email" required 
           autocomplete="email"
           aria-describedby="email-hint">
    <p id="email-hint">We'll only use this to respond to your inquiry.</p>
  </div>
  
  <div>
    <label for="message">Your message</label>
    <textarea id="message" name="message" rows="5" required></textarea>
  </div>
  
  <button type="submit">Send Message</button>
</form>
```

**Key rules:**

- Every `<input>` needs a `<label>` with matching `for`/`id`
- Use `autocomplete` attributes — helps password managers and assistive tech
- Use `aria-describedby` for hints/help text
- Error messages must be associated with the field that has the error
- Don't use placeholder text as the only label — it disappears when you start typing

### 6. Link Text

Links should make sense out of context — screen reader users often navigate by jumping between links:

```html
<!-- ✅ Descriptive link text -->
<a href="/workshops/">View upcoming workshop schedule</a>

<!-- ❌ Generic — meaningless to a screen reader scanning links -->
<a href="/workshops/">Click here</a>
<a href="/workshops/">Read more</a>
```

### 7. Skip Navigation

For keyboard users, add a skip link so they don't have to tab through the entire nav on every page:

```html
<body>
  <a href="#main-content" class="skip-link">Skip to main content</a>
  <header><!-- navigation --></header>
  <main id="main-content">
    <!-- page content -->
  </main>
</body>
```

```css
.skip-link {
  position: absolute;
  top: -40px;
  left: 0;
  padding: 8px;
  background: #000;
  color: #fff;
  z-index: 100;
}

.skip-link:focus {
  top: 0;  /* becomes visible when focused via Tab */
}
```

## Implementation in Eleventy

### Base template (`src/_includes/base.njk`)

Add these to every page automatically:

```html
<!DOCTYPE html>
<html lang="{{ site.language or 'en' }}">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <!-- ... other meta tags ... -->
</head>
<body>
  <a href="#main-content" class="skip-link">Skip to main content</a>
  
  <header>
    <nav aria-label="Main navigation">
      <!-- nav items -->
    </nav>
  </header>
  
  <main id="main-content">
    {{ content | safe }}
  </main>
  
  <footer>
    <!-- footer content -->
  </footer>
</body>
</html>
```

**Critical:** The `lang` attribute on `<html>` tells screen readers which language to use for pronunciation. Never omit it.

### For multilingual sites

If a Pagify user serves both English and Chinese-speaking clients (common in Pagify's target community):

```html
<!-- English page -->
<html lang="en">

<!-- Chinese page -->
<html lang="zh">

<!-- Mixed content — mark the switch -->
<p>We also offer workshops in <span lang="zh">普通话</span> (Mandarin).</p>
```

## Testing Checklist

Run these checks on every Pagify-built site before delivery:

| Check | Tool | Pass criteria |
|---|---|---|
| Color contrast | [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/) | All text meets AA ratios |
| Keyboard navigation | Manual (Tab through site) | Every interactive element reachable and visible |
| Screen reader | VoiceOver (Mac: Cmd+F5) | Page reads logically, images described, forms usable |
| Heading structure | [HeadingsMap extension](https://chrome.google.com/webstore/detail/headingsmap/) | Logical h1→h2→h3 hierarchy, no skips |
| Automated scan | [axe DevTools](https://www.deque.com/axe/devtools/) | Zero critical/serious violations |
| Alt text | View source, search for `<img` | Every image has appropriate alt text |

## For Coaches

When reviewing a site built by an agent, do this 60-second check:

1. **Tab through the site** — can you reach every link and button? Can you see where focus is?
2. **Zoom to 200%** — does the layout still work? Can you read everything?
3. **Check the headings** — right-click → Inspect → search for `<h1`, `<h2>`, etc. Are they in order?
4. **Check images** — right-click any image → Inspect → does it have an `alt` attribute with a real description?
5. **Check contrast** — is any text hard to read? If you squint, it fails.

If any of these fail, the agent didn't finish the job. File an issue.

## Legal Context

In the United States:

- **ADA Title III** has been interpreted by courts to apply to websites of businesses serving the public
- **Over 4,000 web accessibility lawsuits** were filed in 2024 alone
- Small businesses and sole practitioners are not exempt
- WCAG 2.1 AA is the most commonly referenced standard in legal settlements

This isn't theoretical risk for coaches — it's practical. Getting accessibility right at build time (via Pagify templates) is dramatically cheaper than fixing it after a complaint.

## References

- [WCAG 2.2 — W3C](https://www.w3.org/TR/WCAG22/)
- [WebAIM — Introduction to Web Accessibility](https://webaim.org/intro/)
- [The A11Y Project — Checklist](https://www.a11yproject.com/checklist/)
- [axe DevTools](https://www.deque.com/axe/devtools/)
- [Eleventy — Accessibility](https://www.11ty.dev/docs/accessibility/)
