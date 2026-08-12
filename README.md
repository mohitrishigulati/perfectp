# Perfect Placer — website

## What to deploy

**`index.html` + `assets/`** — that's the whole site. Static, no build step, no
dependencies. Drop it on any host (Netlify, Vercel, S3, cPanel, nginx).

It is plain HTML: the page renders and is fully crawlable with JavaScript
disabled. JavaScript only enhances it — page switching, the contact form, the
clients carousel, the mobile menu.

## Preview-only files — do not deploy

| File | What it is |
|---|---|
| `Perfect Placer.dc.html` | Original authoring format. Renders through `support.js`, which downloads React and Babel from `unpkg.com` at runtime and rebuilds the page client-side. Blank page without JS or if unpkg is unreachable. |
| `Perfect Placer (standalone).html` | Bundled export of the same. Still loads React from unpkg. |
| `_pp_standalone_src.dc.html` | Bundler input. |
| `support.js`, `image-slot.js`, `.image-slots.state.json` | The preview runtime and its image store. |

These are kept for reference. `index.html` is the source of truth — edits made
to the `.dc.html` files will **not** appear on the live site.

Their **copy** has been kept in step with `index.html` (stat wording, logo size,
footer email) so they don't mislead anyone reviewing them. They still do **not**
have the responsive layout, the `<head>` metadata, the named form fields, or the
no-JavaScript fallback. Review the site at `index.html`.

## Wiring up the contact forms

Both forms validate and collect data, but nothing is sent anywhere yet. Every
field has a `name`, so going live is a one-line change — set `FORM_ENDPOINT`
near the top of the `<script>` block at the bottom of `index.html`:

```js
var FORM_ENDPOINT = 'https://formspree.io/f/xxxxxxx';
```

Any endpoint accepting a `multipart/form-data` POST works (Formspree, Basin,
Netlify Forms, your own API). The candidate form includes a CV file upload, so
the endpoint must accept file uploads. On failure the form shows an error and
points the visitor at `info@perfectplacer.in`.

Fields posted:

- **Employer** — `form_type=employer`, `full_name`, `email`, `phone`, `company`,
  `designation`, `position`, `headcount`, `message`
- **Candidate** — `form_type=candidate`, `first_name`, `last_name`, `company`,
  `email`, `phone`, `job_title`, `looking_for`, `cv` (file)

## Before going live — please confirm

- **Domain.** `https://www.perfectplacer.in/` is assumed in the canonical link,
  the Open Graph tags and the schema.org block. Search for `perfectplacer.in`
  in `index.html` and correct it if the domain differs.
- **Offices.** The NCR office is called **Delhi** everywhere in the visible copy
  (Contact panel, footer, Why Us card 05, Clients box 2, meta description). The
  schema.org block still carries the precise postal locality, `Noida`, because
  structured data should match the postal record — that is deliberate, not a
  leftover. No office shows a street address on the page; add them if they should.
- **About page image.** The authoring file left that slot empty, so it currently
  renders as a dark brand panel. To use a photo, replace the marked `<div>` in
  the About section with an `<img src="assets/about.jpg">`.

## Local preview

```bash
python -m http.server 8765
```

Then open http://localhost:8765/
