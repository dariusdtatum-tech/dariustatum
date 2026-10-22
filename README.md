# Evenfaring — Studio Site

Evenfaring is a one-person software studio founded by Darius, making calm,
steady software instruments for everyday life. This repo is the studio's
site: one static page, read as a voyage in four legs (Departure, The Open
Sea, Harbors, Arrival) drawn on a nautical chart.

**Live preview:** https://dariusdtatum-tech.github.io/evenfaring/
(GitHub Pages; pushing to `main` redeploys. Pre-launch, so it is not indexed.)

## Run it

No build step. Any static file server works:

```bash
python3 -m http.server
```

Open `http://localhost:8000`. Opening `index.html` directly also works; the
service worker just won't register from `file://`.

## Files

| File | What it is |
|---|---|
| `index.html` | The whole site: markup, styles and script |
| `style.css` | Shared styles for the small static pages (privacy); palette and fonts match `index.html` |
| `sw.js` | Service worker. Bump `evenfaring-studio-vNN` on every deploy (currently v48) |
| `manifest.webmanifest`, `icons/`, `apple-touch-icon.png`, `favicon.svg` | PWA and icons |
| `privacy.html` | Privacy page, kept at the root so its URL stays stable |
| `404.html` | Standalone not-found page |
| `og-image.png` | Link-preview image |
| `robots.txt`, `sitemap.xml` | Crawler rules (see "Pre-launch state") |
| `docs/` | Working documents: App Store guides, site audits, studio roadmap |
| `archive/legacy-site/` | The pre-voyage multi-page site, archived 2026-10-20 |
| `assets/`, `previews/` | Self-hosted fonts and licences, legacy assets and screenshots |

## Locked direction (approved Oct 22, 2026)

Keep this focus. Changes should refine within it, not redirect it.

- **Layout:** one stacked vertical column, max 1120px, one shared left edge.
  Never horizontal, and never widened on desktop. Wide screens keep calm,
  empty margins over the chart; there is no split heading/work layout.
- **Style language:** the shared language of the *Aliveness Playbook* and
  *Experience Study* documents:
  - section heads: a small terracotta letter-spaced label, an upright
    Cormorant Garamond serif title, a short summary line, and a 2px ink rule;
  - portfolio pieces as numbered warm-paper cards ("Portfolio 01" in italic
    terracotta serif), hairline border, 14px radius, soft shadow, with the
    status line in a tinted note;
  - sand callouts (Notices to Mariners, the captain's note) at 10px radius;
  - pill buttons and chips.
  Type: Cormorant Garamond + Plus Jakarta Sans on the 12/14/16/20/32/56 scale.
- **Surface:** Aged Heirloom Vellum Patina is the default
  (`data-patina="aged"`); the documents' paper tones are mixed into it, never
  replacing it. The nautical chart background stays, fixed and full width.
- **Colour:** terracotta (`--timber`) is the action colour.
- **Theme:** Auto by default (follows the system), with Daylight and Night Watch.
- **Sound:** Harbor Audio is off by default; a saved "On" choice is honoured.
- **Voyage log:** the chart room, opened from the Appearance menu: drag,
  zoom and islands, real app statuses, "Last updated" date, principles linked
  to their sources, a harbor with support, privacy and contact, and a text list.
- **Colophon:** restored under its compass star, on the hairline rules.

## Header and Appearance menu

The header holds the brand, four leg links and the Appearance button. Every
control in the menu does something visible:

| Control | What it does |
|---|---|
| Theme: Daylight / Night Watch / Auto | Switches the theme (shown on phones too) |
| Voyage log | Opens the chart room |
| Fair wind | Sails the page hands-free; any scroll takes the helm back |
| Light grade: Dawn / Noon / Dusk / Night | Holds that light over the chart while you scroll; tap it again to return to the scroll-driven day |
| Finish | Toggles Aged Vellum and Fresh Studio; the label names the current finish |
| Harbor Audio | Off / On |
| Workbenches (3) | Opens the workbench views |
| Download HTML | Downloads the page |

Retired on Oct 22, 2026 because their targets were gone: Route, Soundings,
Notes, View (Calm Horizon is the only view), Set sail. Don't bring back a
control unless its target is visible.

## Motion

- At most one living element at a time (two running animations at most, kept
  slow and quiet). No new animations without a reason.
- `prefers-reduced-motion` makes the page static: transitions and animations
  become instant or inert.

## Accessibility and QA bar

Every deploy is checked on the live site (loaded with `?r=random` to bypass
caches) at 390, 1024, 1280 and 1920px, light and dark:

- axe-core: 0 violations (including colour contrast);
- no horizontal overflow (`scrollWidth` ≤ viewport);
- CLS ≈ 0 (layout-shift observer);
- tap targets at least 44px;
- each Appearance control changes something; theme switching verified at 390;
- a fresh visit with no storage gives `data-patina="aged"`, audio off;
- simplicity limits: ≤ 8 font sizes, ≤ 10 spacing values, ≤ 6 background
  colours, ≤ 6 header controls, ≤ 20 visible buttons, ≤ 2 running animations.

The Playwright scripts live outside this repo; the list above is the bar.

## Truthful claims

Copy states only what is true today. No invented numbers, users or dates.
Current status lines:

- **Urge Walk:** In development · iPhone, December 2026 · core features free ·
  $4.99/month premium · no ads
- **Fitness app:** In planning (no date)
- **Christian game:** In planning · all ages

## Pre-launch state

- `<meta name="robots" content="noindex, nofollow">` is on the pages.
- `robots.txt` says `Disallow: /`.
- The contact address is the placeholder `hello@example.com`.
- There is no `CNAME`; the site is served from the github.io URL.
- `404.html` links use the `/evenfaring/` project path.
- The canonical, `og:url`, `og:image` and `twitter:image` tags already point
  at `https://evenfaring.com/`.

## November launch checklist

- [ ] Restore the `CNAME` file (`evenfaring.com`) and set the custom domain in Pages.
- [ ] Remove the `noindex, nofollow` meta tags.
- [ ] Change `robots.txt` to allow crawling and list the sitemap.
- [ ] Replace `hello@example.com` with the real studio address everywhere.
- [ ] Fix the 404 paths: `/evenfaring/…` becomes `/…` on the custom domain.
- [ ] Update the description and link tags (canonical, og, twitter, sitemap)
      to the final evenfaring.com URLs and check the preview image.
- [ ] Bump the service worker cache and run the full QA bar on the domain.
