# Spendly — Public Website

A tiny **static** website (plain HTML/CSS, no frameworks, no JS, no build step)
for the Spendly homepage and legal pages.

```
website/
├── index.html          # Homepage — hero, 3 feature cards, contact, legal links
├── styles.css          # Shared styles (emerald/mint, responsive, dark-mode aware)
├── privacy.html        # FULL Privacy Policy  (served at /privacy or /privacy.html)
├── terms.html          # FULL Terms of Service (served at /terms or /terms.html)
├── privacy/
│   └── index.html      # FULL Privacy Policy  (served at /privacy/)
├── terms/
│   └── index.html      # FULL Terms of Service (served at /terms/)
└── README.md           # This file
```

- **No redirect stubs.** Both the flat files (`privacy.html`, `terms.html`) and
  the folder pages (`privacy/index.html`, `terms/index.html`) contain the **full,
  identical** page. This is deliberate belt-and-suspenders: whatever a host maps
  `/privacy` to (extensionless → `privacy.html`, or folder → `privacy/index.html`),
  the visitor always sees the real page — never a "moved" placeholder. Keep the
  flat and folder copies in sync when the text changes.
- **Links use `/privacy` and `/terms`** everywhere (homepage buttons, nav,
  footer). On Cloudflare Pages / Netlify these clean URLs resolve to the full
  page; `/privacy.html`, `/terms.html`, `/privacy/`, `/terms/` all work too.
- **Absolute asset paths**: pages reference `/styles.css` and link to `/`,
  `/privacy`, `/terms`. View the site **through a local server** (below) —
  opening files via `file://` won't resolve the absolute paths.
- The legal pages still show **"Draft — subject to legal review."** Keep that
  until a lawyer signs off. When the legal text changes, update the
  `../spendly-app/docs/*_WEB.md` sources **and** all four HTML copies.

---

## 1. Preview locally

No build step — serve the folder with any static server (needed because of the
absolute `/` paths):

**Python (built-in on most machines):**
```
cd website
python -m http.server 8080
```
Open:
- http://localhost:8080/
- http://localhost:8080/privacy/
- http://localhost:8080/terms/

**Node:**
```
cd website
npx serve .        # or: npx http-server .
```

---

## 2. Deploy on Cloudflare Pages (recommended)

Cloudflare Pages hosts static sites for free, adds HTTPS automatically, and
serves the folder-based clean URLs (`/privacy`, `/terms`) out of the box.

1. Push this repo to GitHub/GitLab (or use "Direct Upload").
2. Cloudflare dashboard → **Workers & Pages** → **Create** → **Pages** →
   **Connect to Git** (or **Upload assets**).
3. Build settings:
   - **Framework preset:** None
   - **Build command:** *(leave empty)*
   - **Build output directory:** `website`
     (or set the project root to `website/` if uploading just this folder)
4. Deploy and verify the temporary `*.pages.dev` URL (test `/`, `/privacy`,
   `/terms`).
5. **Custom domain:** Pages project → **Custom domains** → add
   **`www.spendlyapp.me`** (and optionally `spendlyapp.me`). Cloudflare shows the
   exact DNS records (see §3).

> Netlify / Vercel / GitHub Pages also work — point the output/publish directory
> at `website/`. Folder-based clean URLs (`/privacy/`) work on all of them.

---

## 3. Connect the GoDaddy domain (`spendlyapp.me`)

**Option A — Move DNS to Cloudflare (simplest, recommended):**
1. In Cloudflare, **Add a site** → `spendlyapp.me` → it gives you **two
   nameservers** (e.g. `xxx.ns.cloudflare.com`).
2. In **GoDaddy** → your domain → **Nameservers** → **Change** → **Custom** →
   paste Cloudflare's two nameservers. (Propagation: minutes to a few hours.)
3. Back in Cloudflare Pages → **Custom domains** → add `www.spendlyapp.me` and
   `spendlyapp.me`; the records are created automatically.

**Option B — Keep DNS at GoDaddy (CNAME to Pages):**
1. Cloudflare Pages → **Custom domains** → add `www.spendlyapp.me`; note the
   target `your-project.pages.dev`.
2. In **GoDaddy → DNS**:
   - `CNAME`: **Name** `www` → **Value** `your-project.pages.dev`
   - Apex `spendlyapp.me`: add a **Forwarding** rule → redirect to
     `https://www.spendlyapp.me` (GoDaddy can't CNAME the root).

---

## 4. URLs that should work after deployment

- `https://www.spendlyapp.me/` — homepage
- `https://www.spendlyapp.me/privacy` — Privacy Policy
- `https://www.spendlyapp.me/terms` — Terms of Service
- `https://www.spendlyapp.me/privacy.html` / `/terms.html` — the same full
  pages (not redirects)
- `https://www.spendlyapp.me/privacy/` / `/terms/` — the same full pages
- `https://spendlyapp.me/*` — should redirect to the `www` versions (set up in §3)

Use `/privacy` and `/terms` as the **Privacy Policy URL** and **Support/Terms
URL** in App Store Connect and Google Play Console.

---

## 5. Keeping content in sync

The HTML legal pages are hand-mirrored from:
- `../spendly-app/docs/PRIVACY_POLICY_WEB.md`
- `../spendly-app/docs/TERMS_OF_SERVICE_WEB.md`

When those change (or after legal review), update **all four** copies so they
stay identical — `privacy.html` + `privacy/index.html`, and `terms.html` +
`terms/index.html` — then redeploy. (Tip: edit the folder page, then copy it over
the flat file.) **Do not** claim any not-yet-live feature (real AI, OCR, voice,
receipt scanning, SMS, Auto-Track, payments/bank connections) is active — keep
the "coming soon" / "planned" framing.
