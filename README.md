# St Peter's Great Totham Bell Ringers — new website

This folder is a complete, ready-to-deploy website for St Peter's Great Totham Bell Ringers. Seven HTML pages, one stylesheet, and all photographs and PDFs grouped into a single `resources/` folder by purpose. The site is fully static — no database, no server-side code — which is what makes it free to host.

To preview the site before deploying, double-click `index.html` and it'll open in your browser. Resize the window down to phone width to see how it reflows on mobile.

---

## What's in this folder

```
7 HTML pages at the root:
  index.html     Home — hero, welcome, practice times, history, news preview, nearby towers
  news.html      Recent stories + monthly newsletter archive
  ringers.html   Past and present ringers, photo gallery, biographies
  bells.html     History, specifications, the 1991 overhaul, the tower
  ringing.html   How change ringing works, plain hunt diagram
  peals.html     Peal boards gallery, 1912 Treble Bob Minor, quarter peals list
  qa.html        Frequently asked questions + contact details

style.css        Single shared stylesheet — change colours/fonts here for whole site
README.md        This file
HANDOFF.md       Design decisions and conventions for editors

resources/       40 images and 49 PDFs, organised by purpose:
  images/peal-boards/   Dated peal-board scans
  images/ringers/       People, events, outings, learners, festivals, Christmas
  images/church/        St Peter's, the bells, ringing chamber, St Leonard's bell
  images/misc/          Polo-shirt silhouette, EACR logo
  documents/newsletters/   Monthly "News from the Belfry" PDFs
  documents/peal-records/  Quarter peals, call changes, rounds, the 1912 peal record
  documents/articles/      Diary of a Novice, biographies, method references
  documents/plans/         The 2020 ringing plan
```

All asset filenames have been normalised — spaces replaced with underscores, the stray double `.jpg.jpg` extension fixed, the obsolete document-icon images removed. The HTML references match the filenames exactly, e.g. `src="resources/images/peal-boards/October26_1895.JPG"`.

---

## Things to fill in before going live

A few placeholders need real content from the band before the site is properly ready to publish:

1. **Tower Captain contact details** — currently `tower.captain@example.com` on `qa.html`. Replace with the band's preferred email and (if they want it published) phone number. The original site published Janice's number openly, but this is a decision for the band.

2. **Names of current ringers** — `ringers.html` says "Today's Band" but doesn't name today's band. Easy to add when you decide what photo and what names go there.

3. **Recent news** — the most recent dated story I have is the Tower Outing of April 2024. Anything since then needs writing up; the structure on `news.html` makes it clear how a new entry is added.

4. **The "since 1895" framing** on the home page is based on the earliest peal board. If the band has earlier records, that line can be tightened up.

You can edit these directly in the HTML files (any plain text editor will do — Notepad, TextEdit, Sublime, VS Code) before uploading, or after deployment via the GitHub web interface (instructions below).

---

## Deploying it: GitHub Pages (recommended, free forever)

This is the simplest path that gives you free hosting, free HTTPS, and a custom domain. The whole process takes about half an hour the first time.

### 1. Create a GitHub account

Go to **github.com** and sign up. The free tier is all you need. Pick a username — it can be the band's name (`greattothambellringers`) or your own.

### 2. Create a new repository

Once signed in:

- Click the **+** in the top right, then **New repository**
- Name it something like `bellringers-site` (the name doesn't matter much)
- Set it to **Public** (private repos can't use Pages on the free tier)
- Tick **Add a README file**
- Click **Create repository**

### 3. Upload the site files

On your new repository's page:

- Click **Add file → Upload files**
- Drag the *contents* of this folder into the browser window — the HTML files, `style.css`, the markdown files, and the `resources/` folder. GitHub will preserve the folder structure.
- Wait for them all to upload (could take a few minutes — there are quite a few PDFs and the total is ~50MB)
- Scroll down, write a commit message like "Initial site upload", and click **Commit changes**

### 4. Turn on GitHub Pages

- On the repository page, click **Settings** (top right)
- In the left sidebar, click **Pages**
- Under "Build and deployment", set Source to **Deploy from a branch**
- Set Branch to **main** and folder to **/ (root)**
- Click **Save**

Wait a minute or two, then refresh the Pages settings page. You'll see a green box saying **Your site is live at https://yourusername.github.io/bellringers-site/**. Click the link — you should see the site.

That's it for free hosting on a `github.io` URL. To use the band's own domain, keep going.

### 5. Connect the custom domain

You'll need to do two things — one in GitHub, one at the domain registrar.

**At the registrar** (wherever you bought the new `.org.uk` domain — Namecheap, 123-reg, Gandi etc.):

Find the DNS settings. You need to add records pointing the domain at GitHub's servers. The four GitHub Pages IP addresses are (verify these are still current at [docs.github.com/pages](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site)):

```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

Add four **A records** for `@` (the root domain) pointing to each of those IPs.

Optionally, also add a **CNAME record** for `www` pointing to `yourusername.github.io.` (note the trailing dot, and it's `.io` not `.io/something`).

DNS changes can take anywhere from a few minutes to a few hours to propagate.

**Back in GitHub:**

- Go to repository **Settings → Pages**
- Under "Custom domain", type `greattothambellringers.org.uk` (or whatever the new domain is) and click **Save**
- Wait a few minutes — GitHub will check DNS
- Once it says "DNS check successful", tick **Enforce HTTPS** (this gives you the free SSL certificate via Let's Encrypt)

Visit the new domain. The site should load with a padlock in the address bar.

---

## Updating the site later

Adding a new "News from the Belfry" entry, fixing a typo, or replacing a photo:

- Go to the GitHub repo in your browser
- Click on the file you want to edit (e.g. `news.html`)
- Click the pencil icon (top right) to edit
- Make the changes
- Scroll down, write a brief commit message ("Add March 2026 newsletter") and click **Commit changes**
- The live site updates automatically within a minute or two

For new images or PDFs, use **Add file → Upload files** as before. Use underscores instead of spaces in filenames to keep things tidy.

If you want a friendlier editing experience that doesn't involve GitHub directly, **Decap CMS** can be layered on top later — it gives a simple admin page where the Tower Captain could write a new article and publish it without ever seeing HTML. That's a future thing, not a deployment thing.

---

## Alternative hosts (if you don't want to use GitHub)

**Cloudflare Pages** ([pages.cloudflare.com](https://pages.cloudflare.com)) — free, generous, very fast, drag-and-drop deploys. Custom domain and HTTPS included. Slightly less common for non-technical handover.

**Netlify** ([netlify.com](https://netlify.com)) — free tier covers this site comfortably. Drag-and-drop the whole folder onto their dashboard and it deploys instantly. Custom domain support is straightforward.

Both of these are excellent and would also give you free HTTPS on a custom domain. The reason I'd lean towards GitHub Pages for a community group is that GitHub is a well-known platform that's likely to outlive any specific person's involvement — there's no risk of "the account holder moved away and we can't get back in".

---

## One last thing — try to recover the old domain first

`greattothambellringers.org.uk` has been live since November 2013 and has incoming links from the Essex Association of Change Ringers, the parish, and probably the achurchnearyou listing. Losing the domain means losing all that.

Before paying for a new one, it's worth one phone call to the registrar (or to Nominet, who manage `.uk` domains, on **01865 332211**) to see whether the band can prove ownership and recover the account. The argument is straightforward: the domain belongs to St Peter's Church Great Totham Bell Ringers, the parish can vouch for it, and the original registrant either no longer rings or can't remember the password. Nominet's account-recovery process is reasonable for genuine cases like this.

If that doesn't work, a fresh `.org.uk` from any reputable UK registrar (Namecheap, Gandi, 123-reg) is around £6–10 per year and registration takes about ten minutes.

---

## Cost summary

| | Recurring |
|---|---|
| Hosting (GitHub Pages) | £0 |
| HTTPS / SSL certificate | £0 (free via Let's Encrypt, automatic) |
| Domain registration | ~£6–10/year (only unavoidable cost) |
| **Total** | **~£8/year** |

That's the entire ongoing cost of the new website. No ads, no surprise bills, no tier downgrades. The £8 a year is just to keep the domain registered.

---

## Files included that aren't currently used

The deployment includes everything from the original archive — including images and PDFs not yet referenced anywhere on the new site. They're kept in case the band wants to draw on them for future news entries:

- `resources/images/misc/Churchforshirtsredbackground.jpg` (the church silhouette logo from polo shirts)
- `resources/images/misc/bell_logo1.gif` (Essex Association of Change Ringers logo)
- `resources/images/ringers/Flowerfestival2016.JPG`, `Flower_Festival_2018_1.jpg` (additional flower festival photos)
- `resources/images/ringers/RingingRemembersphoto2.jpg` (alternative Ringing Remembers photo)
- `resources/images/ringers/Milleniumringers.jpg`, `Millenium_ringers.jpg` (one is likely a duplicate)
- `resources/images/church/St_Peters_Church_2.JPG`, `View_of_Great_Totham_Bells.jpg` (alternative views)
- `resources/images/church/StLeonardsBell.jpg`, `St_Leonards_Bell.jpg` (one is likely a duplicate)
- `resources/images/church/ringingchamber.JPG`, `ringingchamberafterrefurb.JPG` (the ringing chamber)
- `resources/images/ringers/ToweroutingMar15.JPG` (March 2015 outing)
- `resources/documents/articles/GrandsireDoubles.pdf` (method reference)

If you want to keep the deployment lean you can delete these — the site won't break.

---

## Questions, or stuck?

The most common things that go wrong:

- **An image is broken** → check the filename matches exactly (case-sensitive on GitHub Pages — `October26_1895.JPG` is not the same as `october26_1895.jpg`), and that the path in the HTML still points to the right `resources/images/<group>/` subfolder
- **Site looks unstyled** → `style.css` isn't in the same folder as the HTML, or didn't get uploaded
- **Custom domain not working** → DNS hasn't propagated yet (wait an hour) or the A records are typo'd
- **HTTPS option is greyed out in GitHub** → DNS check hasn't completed yet; come back in 10 minutes

Anything else, ask.
