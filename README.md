# Evenfaring — Studio Site

Evenfaring is a one-person independent software studio making calm, steady
software instruments for everyday life.

This is the studio's site. It is a single static page built around one idea:
a captain's voyage. The visitor sails east across four legs — Departure,
The Open Sea, Harbors, Arrival — and meets the studio's instruments,
workbenches, and the system behind the site along the way.

## Run it

No build step. Any static file server works:

```bash
python3 -m http.server
```

Then open `http://localhost:8000`. (Opening `index.html` directly also works;
the service worker just won't register off `file://`.)

This repo is served by GitHub Pages at https://dariusdtatum-tech.github.io/evenfaring/ —
pushing to `main` redeploys the site.

## Files

| File | What it is |
|---|---|
| `index.html` | The whole site — markup, styles, and script |
| `style.css` | The same stylesheet, kept in sync with the inline styles |
| `sw.js` | Service worker (offline support) |
| `manifest.webmanifest` | PWA manifest |
| `icons/` | App icons |
| `privacy.html` | Privacy page — kept live at the root deliberately, so its URL stays stable |
| `404.html` | Standalone not-found page |
| `og-image.png` | Link-preview image |
| `apple-touch-icon.png` | iOS home-screen icon |
| `favicon.svg` | Favicon |
| `robots.txt` | Crawler rules (see "Before launch" below) |
| `sitemap.xml` | Sitemap — homepage and privacy page |
| `docs/` | Working documents: `docs/app-store/` (App Store submission checklists & guides), `docs/audits/` (site audits) |
| `archive/legacy-site/` | The pre-voyage multi-page site pages, archived 2026-10-20 |
| `assets/` + `previews/` | Legacy site assets (self-hosted fonts & licenses, app icons) and screenshots of the old pages, kept for the archive |

## What's on the voyage

- **The voyage itself** — vertical scrolling sails the world eastward; the
  page tips into the chart table as you leave harbor, then settles back.
- **A vessel that sails with you** — screen-fixed, riding a self-drawing
  route with X-marked harbors and a fathoms readout.
- **Dawn to night** — light grades crossfade across the four legs, with stars
  on the final leg.
- **Chart Room** — an explorable archipelago map; click an island to sail
  there, click an islet to step into a workbench.
- **Ship's log** — pinned personal notes, kept on your device only.
- **Tide pool, message in a bottle, rain showers** — small ambient life.
- **Cartographer's lens** — add `?chart` to the URL to see the site's
  construction outlines.
### New in v18 — Refinement

- **Hand-inked edges** — card art frames, the hero chronometer, and chart-room
  islands carry a subtle ink-displacement line; corners are hand-drawn.
- **Route-mask transitions** — leg changes sweep along the route's own shape
  instead of a generic mist.
- **Waterlight** — a slow shimmer band drifting across the chart.
- **Hand-held vessel** — the ship rides summed-sine noise on lift and trim,
  not a metronome bob.
- **Calm springs** — one motion language: a calm ease and a gentle settle,
  on hovers, buttons, modals, and arrivals.
- **Chart-room zoom** — wheel, pinch, `+`/`-` buttons, double-click; pan is
  clamped so the map can't escape the view.
- **Draggable workbench modal** — on desktop, drag it by the topbar like a
  captain's desk; the position resets on open.
- **Living previews** — each instrument card shows a tiny live instrument
  (breathing dot, a sundial that fills with the voyage, cycling suit pips,
  ready/listening status).
- **Refinement checklist** — unified hover/active/focus tokens, sea-glass
  text selection, slim timber scrollbar, hand-checked contrast, `100dvh`
  fallbacks, a composable SCENES registry.

Reduced motion is respected throughout: with `prefers-reduced-motion`, the
voyage still sails, flat and still; every v18 animation goes inert or instant.

## Before launch

- The `robots.txt` in the repo still says `Disallow: /` from the preview
  setup — update it when you're ready for search engines to crawl.
- Supply a real `og-image.png` for link previews.
- Point the "See the three apps" buttons at their real deployment URLs.
