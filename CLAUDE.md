# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Static marketing/community site for "Progetto Esperti Profeta" (fantacalcio — Italian fantasy football community), an Italian-language site for a group that runs fantasy football auctions ("C'è Asta per Te"). No build system, no package manager, no framework — plain HTML files styled with the Tailwind CDN build and deployed as-is (e.g. via GitHub Pages/static hosting).

## Development

There is no build/lint/test tooling. Edit the HTML files directly and open them in a browser (or serve the directory, e.g. `python3 -m http.server`) to preview. Changes go live by committing/pushing — there is no compile step.

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
