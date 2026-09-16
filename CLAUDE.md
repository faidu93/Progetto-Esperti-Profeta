# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Static marketing/community site for "Progetto Esperti Profeta" (fantacalcio — Italian fantasy football community), an Italian-language site for a group that runs fantasy football auctions ("C'è Asta per Te"). No framework — plain HTML files deployed as-is (e.g. via Vercel static hosting). Styling is Tailwind, but as a **compiled static file** (`assets/tailwind.css`), not the runtime CDN build — see "CSS build" below.

## Development

No JS/HTML build or lint/test tooling. Edit the HTML files directly and open them in a browser (or serve the directory, e.g. `python3 -m http.server`) to preview. Changes go live by committing/pushing — there is no compile step for the markup.

## CSS build

`index.html`, `asta.html`, `fantalistone.html`, `iscriviti.html` and `privacy.html` link a single pre-built stylesheet, `assets/tailwind.css` (generated, committed to the repo — not built on deploy). This replaced the Tailwind Play CDN script (`cdn.tailwindcss.com`) that every page used to load, which ships the whole Tailwind engine as JS and recompiles the page's CSS in the browser on every single visit — explicitly flagged by Tailwind itself as unfit for production, and the single biggest thing making the site feel slow to load.

- Source of truth for the design tokens (colors, font families, border radius) is `tailwind.config.js` at the repo root — it used to be duplicated inline (`<script id="tailwind-config">`) in every page.
- `donazione.html` doesn't use Tailwind at all (hand-written inline `<style>`) and isn't part of this build.
- To rebuild after changing `tailwind.config.js` or adding new utility classes to any of the 5 pages above:
  ```
  npm install   # first time only — installs tailwindcss, @tailwindcss/forms, @tailwindcss/container-queries as devDependencies
  npx tailwindcss -i tailwind.input.css -o assets/tailwind.css --minify
  ```
- After rebuilding, bump the `?v=` query string on every `<link href="/assets/tailwind.css?v=...">` tag (one per page) so browsers/CDN caches pick up the new file — same cache-busting convention as the dashboard repos.
- `node_modules/` is gitignored; only the generated `assets/tailwind.css` is committed, so a fresh clone doesn't need Node to just view/edit the pages — only to change styles.

## Pages

- `index.html` — landing page ("Il Progetto Esperti")
- `iscriviti.html` — sign-up page, embeds a Tally.so form for community registration
- `asta.html` — auction listing/registration page (see Auction system below)
- `donazione.html` — standalone redirect page to a PayPal.me donation link (meta-refresh + JS fallback), not styled like the rest of the site
- `privacy.html` — privacy policy

Each page is fully self-contained: the `<head>` (Tailwind CDN script + inline `tailwind.config`), the top nav, the mobile hamburger menu, the page-loader splash, and the `IntersectionObserver`-based `.reveal-on-scroll` animation logic are all duplicated inline in every file rather than shared. When changing the design system, nav links, or shared behavior, the edit must be repeated across `index.html`, `asta.html`, and `iscriviti.html` (grep for the pattern first, e.g. `grep -n "tailwind-config" *.html`).

## Design system

Full rules are in `DESIGN.md` — read it before touching styling. Key points:
- Dark, ink-grey palette (`surface` `#131313`) with a single vibrant orange accent (`primary-container` `#ff6b00`), defined as custom Tailwind color tokens (`surface-container-*`, `on-*`, etc.) inside each page's inline `tailwind.config`.
- **No 1px solid divider borders** for sectioning — use background-color shifts or spacing instead. Borders that do exist should be "ghost borders" (`outline-variant` at ~15% opacity), never fully opaque.
- Two-font system: `font-headline` (Space Grotesk) for display/headlines, `font-body` (Plus Jakarta Sans) for body text.
- Shadows are ambient/soft (`on-surface` at 6% opacity, large blur), never hard drop shadows.

## Auction system (`asta.html`)

This is the most dynamic page. Auctions are defined in a hardcoded JS array `const aste = [...]` (around line 215), one object per auction:

```js
{ id, nome, data, modalita, tallyId, max_posti, quota, sheetName }
```

- `id` — stable numeric identifier (keep unique when adding auctions; note the display name in `nome`, e.g. "Asta #11", is independent of `id` and not always sequential — check existing entries before assigning a new one).
- `tallyId` — the Tally.so form ID embedded in an iframe (`https://tally.so/embed/{tallyId}`) for that auction's registration form. New auctions are sometimes added with a placeholder ID and updated once the real Tally form exists (see commit history).
- `sheetName` — must exactly match the tab name in the backing Google Sheet; used as the key when reading seat counts back from the API.
- `max_posti` — seat cap for that auction (varies: most are 10, some are 12).
- `modalita` — auction mode (`Classic`, `Random`, `Mantra`, `Mantra Euroleghe`, `Draft`); drives the color-coded badge in `renderAste()`.

The `ASTE_ATTIVE` boolean flag near the top toggles between the live auction grid and a "coming soon" countdown placeholder.

Seat counts are fetched at runtime from a Google Apps Script Web App (`API_URL`, a `script.google.com/macros/.../exec` endpoint acting as a read API in front of a Google Sheet) via `fetchCounts()`. That backend is external to this repo — it is not covered by this codebase and must be updated/redeployed separately if the sheet/API contract changes. `updateCounts()` matches each auction's `sheetName` against the API response to render "posti liberi" (free seats) and progressively locks the form once full.

## External integrations

- **Tally.so** — all registration forms (`iscriviti.html` and each auction card in `asta.html`) are embedded Tally forms loaded via `https://tally.so/widgets/embed.js`, referenced only by form ID.
- **Google Apps Script** — read-only seat-count API for `asta.html`, external to this repo.
- **PayPal.me** — `donazione.html` redirects to a hardcoded PayPal.me link.
