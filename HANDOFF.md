# HANDOFF — Great Totham Bell Ringers website

This is a context document for anyone (human or AI) picking up the project. It captures the design decisions, voice conventions, and current state of the site so changes don't drift away from the original intent. Read it before making non-trivial edits.

The user-facing deployment guide is `README.md`. This document is for people working *on* the site, not deploying it.

---

## What this is

A static website for the bell ringers of St Peter's Church, Great Totham, Essex. Replaces an existing site at `greattothambellringers.org.uk` that was built around 2013 in HTML 4.01 with table-based layout, Verdana, no HTTPS, content locked in PDFs, and no mobile responsiveness.

The volunteer rebuilding it has no budget. The site has no dynamic needs — no logins, no comments, no booking system. The plan is GitHub Pages on a custom domain, free hosting plus ~£8/year for the domain.

The audience is split:

- **Visitors curious about the church or its bells** — village locals, family historians, people who hear the bells and look the church up
- **Visiting ringers** passing through Essex looking for nearby towers and contact details
- **Prospective learners** wanting to know how to get involved

The site needs to read as quietly traditional, not corporate, not stuffy. It is a community group's website, not an institution's.

---

## Quick start

```bash
# Preview locally — any of these work
python3 -m http.server 8000
# then visit http://localhost:8000

# or use VS Code's "Live Server" extension on index.html
# or just double-click index.html in the file manager — local file:// URLs work fine for this site
```

Edit any HTML file or `style.css`, refresh the browser, repeat. There is no build step, no compiler, no node_modules, no package.json. By design.

---

## Project structure

The HTML, CSS, and markdown sit at the project root. Images and PDFs are grouped by purpose inside `resources/`:

```
index.html        Home — hero, welcome, practice times, history, news preview, towers
news.html         Recent stories + monthly newsletter PDF archive
ringers.html      Past and present ringers, biographies, photo gallery
bells.html        History, specifications, the 1991 overhaul, the tower
ringing.html      How change ringing works, plain hunt diagram
peals.html        Peal boards gallery, 1912 Treble Bob Minor, quarter peals
qa.html           FAQ + contact section (id="contact" anchor)

style.css         Single shared stylesheet for the whole site
README.md         Deployment instructions
HANDOFF.md        This file

resources/
  images/
    peal-boards/  10 dated peal-board scans (used on peals.html)
    ringers/      People, events, outings, learners, festivals, Christmas, Coronation
    church/       St Peter's, the bells, ringing chamber, St Leonard's bell
    misc/         Polo-shirt silhouette, EACR logo
  documents/
    newsletters/  Monthly "News from the Belfry" PDFs (news.html archive)
    peal-records/ Quarter peals, call changes, rounds, the 1912 peal record
    articles/     Longer-form pieces — Diary of a Novice, biographies, method refs
    plans/        The 2020 ringing plan
```

The flat-at-root structure for the HTML and CSS is deliberate. Reasoning:

- Easier for non-technical maintainers to understand and edit on GitHub's web interface
- One mental model: `index.html` is at the top, like every other site they've ever opened
- GitHub Pages serves from `/` natively — no build step required
- Cross-page links between HTML files stay as bare filenames (`href="news.html"`)

Images and PDFs are grouped into `resources/` subfolders because there are ~90 of them — flat would make them hard to find, and the categories (peal-boards, ringers, church, newsletters, peal-records, articles, plans) match the editorial categories used on the site. HTML references them as `src="resources/images/<group>/<file>"` and `href="resources/documents/<group>/<file>"`.

### Page structure pattern

Every HTML page follows the same skeleton:

```html
<head>
  Google Fonts preconnect + link
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <header class="site"> ... primary nav, with .current on the active page ... </header>

  <!-- Either:    Home page only -->
  <section class="hero"> ... full-bleed church image ... </section>

  <!-- Or:        Sub-pages -->
  <section class="page-hero"> ... typographic header with eyebrow + h1 + lede ... </section>

  <section class="band">           ... cream background ... </section>
  <section class="band alt-band">  ... paper background, top/bottom rules ... </section>
  ...

  <footer class="site"> ... three columns, ink background ... </footer>
</body>
```

Sections alternate `band` (cream) and `band alt-band` (paper) to give a quiet visual rhythm without anything shouting.

### Header and footer are duplicated in every HTML file

This is intentional — there's no template engine. If you change the nav or footer, find-and-replace across all 7 HTML files. It's irritating but explicit, and means every page is independently understandable. If this becomes painful, the right next step is **Eleventy** (single static-site generator, no JavaScript framework, generates plain HTML) — not React, not Next.js, not Astro.

---

## Design system

The aesthetic is "quietly heritage editorial" — closer to a National Trust handbook or parish magazine than to a startup landing page. Restrained, serif, considered. A small number of details are working hard.

### Typography

Two Google Fonts, both serif:

- **Cormorant Garamond** — display font, used for the brand wordmark, all headings, and large feature numbers. A Garamond revival with strong italics. Weights used: 500, 600. Italics for emphasis and certain labels.
- **Lora** — body font, used for paragraphs, navigation, captions, eyebrows. A friendly book-weight serif. Weights used: 400, 500.

Both serif is unusual — most websites pair a serif display with a sans body. The all-serif choice is intentional and reinforces the parish-magazine feel. **Don't replace either with Inter, Roboto, system-ui, or any sans-serif.** If a font ever needs to change, both should change together to maintain pairing.

### Colour

All defined as CSS custom properties at the top of `style.css`:

```css
:root {
  --cream: #f3ebdc;        /* warmer page background */
  --paper: #faf6ec;        /* slightly lighter, used for alt-band sections */
  --ink: #1f1a14;          /* deep brown-black, primary text and dark surfaces */
  --ink-soft: #4a4034;     /* secondary text, captions */
  --burgundy: #6e2424;     /* THE accent colour */
  --burgundy-deep: #511919;/* hover state for burgundy buttons */
  --brass: #a98246;        /* small ornament colour, footer link hovers */
  --stone: #ddd2bd;        /* light alternate, footer text */
  --rule: #c9bea7;         /* hairline rule colour */
}
```

**The burgundy is doing real work.** It was chosen specifically because it's the colour Victorian peal-board lettering is traditionally painted in. The peal boards in St Peter's tower (visible on `peals.html`) use this same family of red. Using it as the site's accent ties the design to the actual physical objects in the building. **Don't switch the accent to blue or green or anything fashionable.**

To recolour the whole site, change the variables. To recolour one component, override the variable on that component, don't introduce a new hex code.

### The eyebrow pattern

Above every section heading there's a small uppercase label flanked by hairline rules:

```html
<div class="eyebrow">Welcome</div>      <!-- left-aligned -->
<div class="eyebrow center">Welcome</div>  <!-- centered -->
```

Renders as: `─── WELCOME ───`

This is the signature design detail. It deliberately echoes the lettering style on Victorian peal boards (small capitals, separator rules). Use it for every section label. Don't introduce alternative eyebrow patterns.

### Sticky header

The site header (`header.site`) is sticky on scroll across every page — `position: sticky; top: 0; z-index: 50;` with a 1px `box-shadow` standing in for the old border-bottom so the hairline still reads. The header background is opaque (`var(--paper)`) so content scrolling underneath doesn't bleed through. No JavaScript involved; CSS handles it. Because the header is always visible, don't add a "back to top" button — it would be redundant.

### Heroes

Two kinds:

- **`<section class="hero-spread">`** — magazine-spread layout, used **only** on the home page. Photograph of the church on the left (an `<img>` element with a real `alt`, not a CSS background — better for accessibility and SEO), welcome typography on cream to the right. The whole church is visible in the photo: spire, weather vane, flint walls, porch, gravestones. Title set in Cormorant with the word "Bell Ringers" in burgundy italic.
- **`<section class="page-hero">`** — typographic, used on every sub-page. Eyebrow, large title, italic lede, no image. Keeps the homepage hero special and stops the site feeling repetitive.

If a future page wants imagery in its hero, work it into a section *below* the `page-hero` block, not inside it. The page-hero stays typographic.

### Layouts

Four reusable patterns, all defined in `style.css`:

| Class | Purpose |
|---|---|
| `.container` | Max-width 1180px wrapper |
| `.medium` | 880px wrapper for long-form text content |
| `.narrow` | 720px wrapper for centred copy blocks |
| `.visit-grid` | Two-column grid, used for "Practice & Visiting" + "Find Us" pairs |
| `.two-up` / `.two-up.equal` | Two-column section, 1.1fr/1fr or 1fr/1fr |
| `.photo-grid` | Auto-fill grid, 220px minimum, for ringer photos |
| `.peal-grid` | Auto-fill grid, 220px minimum, for clickable peal-board cards |
| `.qp-list` | Two-column auto-fill list for quarter peal records |
| `.archive-grid` | Auto-fill grid for newsletter PDF links |
| `.bell-table` | Specifications table, used on `bells.html` |

All collapse to single-column at 800px and below.

### Motion

There is almost no motion. Hover transitions on links and buttons (`.2s` colour swaps) and that's it. **Don't add scroll-triggered reveals, parallax, intersection-observer animations, fade-ins, or any framer-motion-style flourishes.** They would actively work against the design intent. Restraint is the aesthetic.

### Responsive

Two breakpoints, plus one deliberate exception:

```css
@media (max-width: 900px) { /* HOME HERO ONLY — magazine spread stacks photo above text */ }
@media (max-width: 800px) { /* tablets, large phones — collapse multi-col grids */ }
@media (max-width: 540px) { /* small phones — stack the nav */ }
```

The 900px breakpoint exists only to stack `.hero-spread` early. The site's standard collapse point is 800px and that should stay the default for any new layout — but the home hero's title needs more horizontal room than other content (three lines of "St Peter's / Great Totham / Bell Ringers" in Cormorant), and at 800–900px it would have wrapped awkwardly inside the right-hand column. The 100px exception is the cleanest fix; don't generalise it to other components.

Everything uses `clamp()` for fluid type scaling between these. Test at 320px (very narrow phone), 540px, 800px, 900px (the hero-stack point), and 1180px+ when making changes.

---

## Voice and content conventions

The text was deliberately rewritten from the original site to be readable, specific, and warm without being twee. Maintain this voice in any new content.

### Specific guidance

- **UK English throughout.** "Practise" (verb) vs "practice" (noun). "Colour", "centre", "organised", "honoured". When in doubt: how would a parish magazine in Essex spell it.
- **Dates as "16th April 1912"**, not "April 16, 1912" or "16/4/1912".
- **Times as "7.45pm to 9.00pm"**, not "19:45–21:00" or "7:45 PM".
- **Place names with full punctuation** — "St Peter's", "St Mary's, Maldon", "All Saints, Inworth". Apostrophes matter; the original site dropped them inconsistently.
- **Specifics over generics.** "A peal of 5,040 changes of Treble Bob Minor in 2 hours and 42 minutes" reads better than "a remarkable peal". Numbers, dates, names, places do the work.
- **Faithful transcription** of quoted material — Janice's Covid email is reproduced as she wrote it, including her phrasing.
- **No marketing speak.** "Come and join us" is the warmest CTA on the site. No "Discover the joy of campanology" or "Unleash your inner ringer".

### Things that have been silently fixed

The original site had typos that I corrected on the way through — "tennor" → "tenor", "begining" → "beginning", "tinging" → "ringing", "succees" → "success". These corrections should stay corrected. If new content arrives via copy-paste from elsewhere, watch for the same kind of thing.

### What we never use

- Emojis
- Exclamation marks (except inside faithfully-quoted material)
- "Click here" as link text
- The word "campanologist" except where it's already idiomatic in a quoted source (e.g. "Diary of a Novice Campanologist" is a real article title)
- The word "exciting"

### What we use sparingly

- Bold emphasis — most paragraphs have none
- Italic emphasis — for honest emphasis, foreign phrases, technical terms on first use, and quoted speech
- Block quotes / pull quotes — only where they actually elevate the content, not for decoration

---

## State: what's done, pending, and to verify with the band

### Done

- All 7 pages built with consistent design language
- Real content drawn from the original site (rewritten where helpful), 1911 census details, the 1912 peal record, the bell specifications, the ringer biographies, the Covid story, the Coronation, the Queen's funeral
- All 10 historical peal boards on `peals.html` as a clickable gallery linking to full-size JPGs
- Newsletter archive linking to all existing PDFs
- Asset filenames normalised — spaces removed, double `.jpg.jpg` extension fixed, design-icon images dropped
- Plain hunt diagram on `ringing.html` rebuilt as a styled HTML table (was a `border=1` table in the original)
- Bell specifications as a properly-styled table on `bells.html`
- Mobile responsive at all breakpoints
- All cross-page links verified

### Pending — needs band input

These are flagged in the README too. Each is a small piece of work once the information is available:

1. **Tower Captain contact email** — `qa.html` has `tower.captain@example.com` as a placeholder. The original site published Janice Spalding's phone number (01621 892197) openly; the band needs to decide what they want public. The footer also has a contact-related anchor (`#contact`) on `qa.html`.

2. **Names of current ringers** — `ringers.html` has a section called "Today's Band" with practice details and four photos (Christmas 2023, Christmas 2013, two learners) but no names of the current band. If the band wants a named lineup with a recent group photo, that's a small addition.

3. **News after April 2024** — the most recent dated story I had access to was the Tower Outing of 13th April 2024. Anything since then needs writing up. Use the existing `<article class="story">` structure on `news.html` as a template — `news-date`, `h3`, `<p>` paragraphs, optional `<figure>` with caption.

4. **The "since 1895" claim** on the home page — based on the earliest peal board (`October26_1895.JPG`). If parish records show ringers earlier than that, the home-page line should be tightened.

### Pending — technical

- **Deployment hasn't happened yet.** The plan is GitHub Pages (see `README.md`) on a custom domain. Recovery of the existing `greattothambellringers.org.uk` should be attempted via Nominet (01865 332211) before paying for a new domain.
- **No analytics installed.** The original site had a StatCounter snippet; I removed it. If the band wants visitor counts, the simplest privacy-respecting option is GoatCounter (free for non-commercial). Don't add Google Analytics without a clear reason — it brings GDPR consent obligations.
- **No favicon.** Add a `favicon.ico` (or 32×32 PNG referenced in `<link rel="icon">`) when the band has chosen one. The polo-shirt church silhouette in `Churchforshirtsredbackground.jpg` could be the basis if cropped tight.
- **No `<meta property="og:image">`** for social sharing. Add when the band cares about how the site looks shared on Facebook / WhatsApp.

---

## Constraints — things NOT to do without a deliberate reason

These have been thought about and decided against. They aren't oversights.

- **Don't introduce a CSS framework** (Tailwind, Bootstrap, Bulma). The custom CSS is ~620 lines, fully understood, and fits the design intent. A framework would either need extensive overrides to match the aesthetic, or would force the site to look like every other framework site.
- **Don't introduce a JS framework** (React, Vue, Svelte). The site has no interactive state. Adding a framework would mean a build step, a hosting platform that supports SSR/SSG, and a maintenance burden that defeats the "free hosting forever" goal.
- **Don't introduce a build step at all** unless absolutely necessary. The site is editable on GitHub's web UI by anyone — including the eventual non-technical maintainer. A build pipeline breaks that.
- **Don't replace the fonts.** Cormorant Garamond + Lora are the design.
- **Don't add icon libraries** (Lucide, Font Awesome, Heroicons). The eyebrow rules are the only "icons" the design uses, and they're CSS pseudo-elements.
- **Don't add scroll animations or page transitions.**
- **Don't add a cookie banner** — the site sets no cookies. If analytics is added later, choose a tool that doesn't require consent (GoatCounter, Plausible, Fathom).
- **Don't move to a database-backed CMS.** If editing HTML directly becomes a pain for the band, the right next step is **Decap CMS** (formerly Netlify CMS) — it adds a friendly admin UI on top of static files committed to Git. No server, no database.
- **Don't paginate the news page** until there's enough content to need it. Currently 10 stories on one page is comfortable.

---

## Working with the codebase

### Adding a news article

1. Open `news.html`
2. Find the section titled `<!-- recent stories -->` (in the first `<section class="band">` after the page hero)
3. Copy an existing `<article class="story">` block as a template
4. Update the `news-date`, `h3`, paragraphs, and any figure
5. Any new image goes in `resources/images/ringers/` (or `resources/images/church/` if it's of the building/bells); reference it as `src="resources/images/<group>/<filename>"`
6. Place new articles **at the top** of the list — most-recent-first ordering
7. If the new article is a major event, also update the "Recently in the Belfry" preview card on `index.html`

### Adding a peal board

1. Add the JPG to `resources/images/peal-boards/` (web-friendly filename — underscores not spaces)
2. Open `peals.html`, find the `<div class="peal-grid">`
3. Insert in date order — newest at the end, since the gallery flows top-left to bottom-right
4. Use the existing `<a class="peal-card">` structure exactly — both the `<a href>` and `<img src>` point to `resources/images/peal-boards/<filename>` (no thumbnail/full-size split needed at this scale)

### Adding a quarter peal or other peal record

1. Add the PDF to `resources/documents/peal-records/`
2. Open `peals.html`, find the `<ul class="qp-list">`
3. Insert a `<li>` with `href="resources/documents/peal-records/<filename>"` in date order

### Adjusting design tokens

All colours, max-widths, and shared values are at the top of `style.css` in the `:root` block. To recolour the whole site, change variables there. Don't search-and-replace hex codes.

### Adding a new page

1. Copy any existing sub-page (e.g. `qa.html`) as the starting point
2. Update `<title>` and `<meta name="description">`
3. Update the nav: add the new link to the `<nav class="primary">` block in *every* page (and add `class="current"` only to the link on the new page itself)
4. Update the footer's `<h4>Pages</h4>` list, again on *every* page
5. Use `<section class="page-hero">` for the page header — eyebrow + h1 + lede

### Filename conventions

- Underscores, not spaces: `Tower_Outing_2024_Photo.jpg`
- Original case preserved (GitHub Pages is case-sensitive — `October26_1895.JPG` and `october26_1895.jpg` are different files)
- New images compressed before adding (TinyPNG, ImageOptim) — keep individual files under 500KB where possible. Some of the existing peal-board scans are large because they need to be readable; that's fine.

---

## Deployment plan

Full instructions are in `README.md`. Summary:

1. **Recover the existing domain first** — `greattothambellringers.org.uk` has been live since 2013, with incoming links worth preserving. Phone Nominet on 01865 332211 if the registrar account password is lost.
2. **Push this folder to a public GitHub repo.** Either via the web UI (drag and drop) or `git push` from VS Code.
3. **Enable GitHub Pages** in repo settings, branch `main`, folder `/`.
4. **Add the custom domain** in Pages settings, configure A records at the registrar pointing to GitHub's four IPs (185.199.108–111.153).
5. **Tick "Enforce HTTPS"** once DNS check passes — Let's Encrypt cert is automatic.

Total ongoing cost: ~£8/year (domain only). Hosting and HTTPS are free.

---

## Open questions for the band, when you next see them

These don't block deployment but should eventually be answered:

- Do they want a contact form (requires a third-party service like Formspree — free tier exists), or is `mailto:` sufficient?
- Do they want visitor analytics? If yes, GoatCounter is the recommended privacy-friendly option.
- Are there scans of older peal records that aren't currently photographed? The 1905–1912 cards in the picture frame on the coat hooks are referenced but not scanned.
- Would they like a small recent group photo for the Ringers page, named?
- Is the EACR logo (`bell_logo1.gif`) something they'd want as a small footer-corner affiliation badge, or kept off the design?

---

If anything in this document is wrong because the design has moved on, update it. It's the source of truth for "why is the site like this", and it's only useful if it stays current.
