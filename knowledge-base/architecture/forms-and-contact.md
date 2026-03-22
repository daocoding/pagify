# Forms & Contact Handling for Static Sites

> Last verified: 2026-03-22

Every website needs a way for visitors to get in touch. Static sites don't have a backend server to process form submissions — so you need a service or technique to bridge the gap.

This guide evaluates practical options for coaches helping non-technical people add forms to their Pagify-built sites.

---

## The Problem

Static HTML sites (deployed on Cloudflare Pages, Netlify, etc.) have no server-side code. When a visitor fills out a contact form and clicks "Submit," there's nowhere for the data to go — unless you wire it up to an external service.

**What coaches need:** A solution that's free (or very cheap), requires no backend coding, and just works.

---

## Option 1: Formspree (Recommended for Most Cases)

**What it is:** A form backend service. Point your HTML form's `action` attribute at Formspree, and submissions get emailed to you.

**Free tier:** 50 submissions/month. Enough for most small sites.

**Setup:**

```html
<form action="https://formspree.io/f/{form-id}" method="POST">
  <label for="name">Name</label>
  <input type="text" id="name" name="name" required>

  <label for="email">Email</label>
  <input type="email" id="email" name="email" required>

  <label for="message">Message</label>
  <textarea id="message" name="message" required></textarea>

  <button type="submit">Send</button>
</form>
```

**Pros:**
- Zero JavaScript required — works with pure HTML
- Spam filtering built in (reCAPTCHA optional)
- Email notifications on every submission
- Dashboard to view submissions
- Custom redirect after submission

**Cons:**
- 50/month limit on free tier (paid plans start ~$10/month)
- Formspree branding on free tier confirmation page (custom redirect removes this)

**Coach tip:** Create the Formspree account under the client's email so they own it.

---

## Option 2: Cloudflare Workers (For Technical Coaches)

**What it is:** Serverless functions that run on Cloudflare's edge. If you're already on Cloudflare Pages, you can add a Worker to handle form submissions.

**Free tier:** 100,000 requests/day. Wildly generous.

**Setup:** Requires writing a small JavaScript Worker function (~20 lines) that receives the form POST, validates it, and forwards via email (using a service like Mailgun or Resend).

**Pros:**
- No third-party form service needed
- Unlimited submissions on free tier
- Full control over validation and processing
- Same platform as your hosting (Cloudflare)

**Cons:**
- Requires JavaScript knowledge to set up
- Still needs an email-sending service (Resend free tier: 100 emails/day)
- More moving parts to maintain

**When to use:** Only if the coach is comfortable writing and maintaining a Worker, or if the client needs more than 50 submissions/month.

---

## Option 3: Mailto Link (Simplest, No Service Needed)

**What it is:** A plain `mailto:` link that opens the visitor's email client.

```html
<a href="mailto:contact@example.com?subject=Website%20Inquiry">Email Us</a>
```

**Pros:**
- Zero dependencies — no service, no account, no limit
- Works forever — no service to shut down or change pricing
- The client understands it immediately

**Cons:**
- Not a "form" — visitors must have an email client configured
- No structured data (you get whatever they type)
- Doesn't work well on shared/public computers
- Looks less professional than an embedded form

**When to use:** When the client just wants an email link and doesn't need structured intake.

---

## Option 4: Google Forms Embed

**What it is:** Create a Google Form, embed it in your page with an `<iframe>`.

```html
<iframe
  src="https://docs.google.com/forms/d/e/{form-id}/viewform?embedded=true"
  width="100%" height="800" frameborder="0">
  Loading…
</iframe>
```

**Pros:**
- Free, unlimited submissions
- Responses go to a Google Sheet automatically
- Familiar UI for many people

**Cons:**
- Looks like Google, not like your site (limited styling)
- Iframe can feel clunky on mobile
- Depends on Google account — client needs one
- SEO-invisible (content inside iframe isn't indexed)

**When to use:** When the client already uses Google Workspace and wants spreadsheet-based response tracking.

---

## Spam Prevention

Whichever option you choose, add basic spam protection:

### Honeypot Field (No JS Required)

Add a hidden field that humans won't fill out but bots will:

```html
<input type="text" name="_gotcha" style="display:none" tabindex="-1" autocomplete="off">
```

If the field has a value on submission, it's a bot. Formspree supports this natively with `_gotcha`.

### Rate Limiting

If using Cloudflare Workers, add rate limiting via Cloudflare's built-in rules — no code needed. Set it to something reasonable like 10 submissions per IP per hour.

---

## Accessibility Checklist for Forms

- Every `<input>` has a `<label>` with matching `for`/`id` attributes
- Required fields use the `required` attribute (not just a red asterisk)
- Error messages are associated with fields via `aria-describedby`
- Form can be completed entirely by keyboard (Tab, Enter)
- Submit button has clear, descriptive text ("Send Message" not just "Submit")

---

## Recommendation for Coaches

**Default choice: Formspree.** It works with pure HTML, requires no coding beyond copy-pasting the form action URL, and handles spam filtering. Set up the account under the client's email.

**If the client just needs an email link:** Use `mailto:`. Don't over-engineer it.

**If you're technical and the client needs volume:** Cloudflare Worker + Resend.

**Avoid** building custom backends, PHP scripts, or self-hosted form processors. The whole point of Pagify is that the output is maintainable by non-technical people. A form service keeps that promise.
