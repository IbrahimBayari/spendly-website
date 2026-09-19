# Spendly — Public Website

A tiny **static** website (plain HTML/CSS, no frameworks, no JS, no build step)
for the Spendly homepage and legal pages.

## 🟢 Status: DEPLOYED and LIVE (2026-07-16) — ⚠️ REDEPLOY PENDING (2026-09-18)

- **Hosting:** Cloudflare Pages (active) · **Domain:** `spendlyapp.me` (connected)
- **Live and verified:**
  - `https://spendlyapp.me` — homepage
  - `https://spendlyapp.me/privacy` — full Privacy Policy
  - `https://spendlyapp.me/terms` — full Terms of Service
  - `https://spendlyapp.me/privacy.html` — full policy (no redirect stub)
- **Canonical URLs are the APEX** (`https://spendlyapp.me/...`). Use these in
  App Store Connect and Google Play Console.
- ⚠️ **Known issue:** `https://www.spendlyapp.me` returns **HTTP 522**
  (Cloudflare cannot reach the origin). Non-blocking — the apex works — but fix
  it so visitors typing `www` don't hit an error. See §3.
- Legal pages still show **"📝 Draft — subject to legal review."** — correct;
  it stays until a lawyer signs off.
- ⚠️ **The files in this folder are AHEAD of what is live.** On 2026-09-18 the
  legal pages were rewritten for real AI: the policy now describes the AI
  provider (OpenAI, United States), the bounded conversation window, the
  in-app consent, how to withdraw it, planned subscription processing, and in-app account
  deletion. **The live site still serves the old text, which says cloud AI is
  "not enabled yet" — that is now false.** Redeploy this folder to Cloudflare
  Pages and re-verify all four URLs before the app goes to external beta.

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

## 3. Domain (`spendlyapp.me`) — CONNECTED ✅

The domain is **already connected and serving** on the apex
(`https://spendlyapp.me`). The setup steps below are kept for reference.

### ⚠️ Outstanding: fix the `www` subdomain (HTTP 522)

`https://www.spendlyapp.me` currently returns **522** while the apex works. A
522 means Cloudflare has DNS for `www` but can't reach an origin behind it —
typically the `www` hostname isn't attached to the Pages project. To fix:

- **Preferred:** Cloudflare Pages project → **Custom domains** → add
  **`www.spendlyapp.me`** alongside the apex. Pages then serves both.
- **Or:** add a **Redirect Rule** sending `www.spendlyapp.me/*` →
  `https://spendlyapp.me/$1` (301), and remove any stale `www` DNS record that
  points at a dead origin.

Until it's fixed, **link and submit the apex URLs only**.

### Reference — original connection steps

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

## 4. Live URLs

**Verified live (2026-07-16):**

- ✅ `https://spendlyapp.me/` — homepage
- ✅ `https://spendlyapp.me/privacy` — Privacy Policy
- ✅ `https://spendlyapp.me/terms` — Terms of Service
- ✅ `https://spendlyapp.me/privacy.html` — the same full page (not a redirect)
- `https://spendlyapp.me/terms.html`, `/privacy/`, `/terms/` — the same full
  pages (all four copies are real pages, per the note at the top)
- ⚠️ `https://www.spendlyapp.me/*` — **HTTP 522**, not usable yet (see §3)

**For the stores:** use the **apex** URLs — `https://spendlyapp.me/privacy` as
the **Privacy Policy URL** and `https://spendlyapp.me/terms` as the
**Support/Terms URL** in App Store Connect and Google Play Console. Do **not**
submit the `www` variants while they 522. Support contact:
**`hello@spendlyapp.me`**.

---

## 5. Keeping content in sync

The HTML legal pages are hand-mirrored from:
- `../spendly-app/docs/PRIVACY_POLICY_WEB.md`
- `../spendly-app/docs/TERMS_OF_SERVICE_WEB.md`

When those change (or after legal review), update **all four** copies so they
stay identical — `privacy.html` + `privacy/index.html`, and `terms.html` +
`terms/index.html` — then redeploy. (Tip: edit the folder page, then copy it over
the flat file.) **Do not** claim any not-yet-live feature is active. As of 2026-09-18 **real AI
is live; production payments are not yet enabled** and the pages say so; **OCR, voice, receipt scanning,
SMS and Auto-Track are still simulated** and must keep the "coming soon" /
"planned" framing. `preview.html` embeds a full copy of both policies too —
update it in the same pass or it becomes a public page contradicting the real
one.
