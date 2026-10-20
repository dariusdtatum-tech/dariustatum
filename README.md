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

Reduced motion is respected throughout: with `prefers-reduced-motion`, the
voyage still sails, flat and still.

## Before launch

- Remove the `robots` `noindex` flag from `index.html`.
- Supply a real `og-image.png` for link previews.
- Point the "See the three apps" buttons at their real deployment URLs.
