# Site audit — 2026-10-21 (live-state review + real-device findings)

Two parts. Part 1 is a live-state review of the repo and the served site
(verified against dariusdtatum-tech.github.io/evenfaring on 2026-10-21, no
changes made during the review). Part 2 is Darius's real-device verdict on
v18, which outranks every emulator check that came before it, plus the v19
fixes it produced.

## Part 1 — the twenty (live-state review)

Launch & discovery
1. robots.txt still says `Disallow: /` — all crawlers turned away (verified live).
2. og:image / og:url point at evenfaring.com, which does not resolve yet — link previews blank today, though og-image.png is served fine on the github.io address.
3. The domain itself: evenfaring.com unpurchased; items 2 and 4 stay half-broken until it exists.
4. sitemap.xml lists only the two evenfaring.com URLs while robots blocks the real ones.

Links & content
5. The site never links to the actual apps — four anchors total; Urge Walk's own live site is unlinked.
6. The brand lockup is `href="#"`, functional only via JavaScript.

Performance & code health
7. style.css is a dead twin: the page renders from inline `<style>` only, yet the 132 KB external file is hand-synced "by convention" and even precached by the service worker. One source of truth would end the drift risk. (sw.js header said "(v11)" while its cache was v18 — fixed in v19.)
8. The document is 482 KB — 28 inline SVGs, all CSS and JS in one file; first paint on mobile data pays for all of it.
9. Google Fonts is the site's only third-party request — visitor IPs go to Google on every load, next to a privacy page that says nothing is collected. Self-hosting removes it (the legacy archive shows the fonts were self-hosted once).
10. See item 7 (stale sw.js version label — resolved in v19).

Repo hygiene
11. `previews/` (~0.5 MB of old-page screenshots) is deployed publicly in the site root; git history already preserves them.
12. The README file map is stale: `assets/` described as legacy-only; the compass brand masters now living there are undocumented.
13. docs/audits held only the v10 audit; verification records lived outside the repo. (This file begins closing that gap.)

Verification gaps
14. v18 had never had a real-phone verdict from Darius. → Resolved by Part 2 — the verdict was "broken."
15. No full accessibility re-audit since the v11 pass, despite five versions of new machinery since.
16. The custom 404 exists but is 1 KB and minimal.

Decisions & resilience
17. Audio wake: sound starts only on click/tap/keypress; the scroll-wake question from 2026-10-20 remains open.
18. No feedback signal at all — by design (privacy), but the voyage experiment has no instrument. Darius's call.
19. No favicon.ico fallback (SVG only); older browsers/crawlers get a 404 for /favicon.ico.
20. No backup: site, docs, roadmap, and brand masters live in exactly one GitHub account.

## Part 2 — real-device findings (Darius, iPhone + PC, 2026-10-21)

21. **Mobile scroll is broken on a real phone.** The page scrolls both
    vertically and horizontally, then gets stuck vertically. Root causes,
    code-verified: (a) `touch-action:none` on the first screen's biggest
    elements — the FIG. 00 chronometer SVG and the shoreline graphic — so a
    swipe starting on them can never scroll the page; (b) the scroll runway
    is rebuilt on every viewport resize (iOS URL-bar show/hide), remapping
    progress under the user's thumb mid-gesture; (c) the 0.14 scroll lerp
    fights iOS momentum. Emulator checks passed because programmatic
    scrolling bypasses touch entirely.
22. **Specimen cards overflow the phone viewport** — neighbours bleed in
    from the right; fixed bars (bottom dock, pin bar) overlap card content.
23. **FIG. 00 (Solar Meridian & Tidal Chart) is impaired on PC** — the
    plate is clipped at its right edge and its footer text passes under the
    fixed vessel readout pill. Separately, the Pin-a-note fab and the legnav
    pill occupied the same bottom-right corner, stacked into one floating
    blob over content on both platforms. Prior audits verified the pin bar
    functioned, never its placement — a recorded blind spot.

### v19 fixes (commit c3832ea4, live 2026-10-21)

- **Mobile (≤860px) is no longer a horizontal voyage.** initVoyage detects
  small screens and returns early into a stacked mode: the stage is static,
  the world a vertical column, legs stack in order, cards are hard-bounded
  to the viewport (the card rails wrap; nothing parks off-screen), the
  vessel/route/legnav hide, the Pin-a-note button sits in the page flow,
  and the chronometer uses `touch-action: pan-y` so vertical swipes scroll
  while horizontal drags still scrub. The horizontal voyage is desktop-only.
- **Desktop voyage hardened:** resize handling debounced so the runway is
  not rebuilt mid-gesture; the vessel readout fades out while the FIG. 00
  plate is beneath it (p < 0.045); legnav moved to the bottom-left corner
  and the Pin-a-note fab keeps the bottom-right — they no longer collide.
- Service-worker cache bumped to evenfaring-studio-v19; its header label
  corrected.

### Verification standard (changed permanently)

Harness checks (geometry sweeps, programmatic scroll journeys, overlap
bounding boxes) are necessary but never sufficient. No "mobile clean" claim
is made from emulation again: acceptance for mobile changes is Darius's
phone. v19 harness result at ship time: 11/11 checks passed; the phone
re-verdict is the one that counts.
