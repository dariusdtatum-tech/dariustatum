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

## Part 3 — Desktop clarity batch (Darius's screenshots, later on 2026-10-21; fixed in v19.3)

24. **FIG. 00 plate renders with chromatic fringe; specimen card tops are
    soft.** Root cause: one shared mechanism. The v18 hand-inked outline
    effect (SVG filter #ink-edge: feTurbulence + feDisplacementMap, scale 3)
    was applied to the WHOLE Solar Meridian plate (.chronometer-card.inked)
    and to every specimen card's header strip (.specimen-plate.inked). The
    filter warps text and thin SVG lines; on a real display it reads as
    colour fringing on the plate and mush in the small-caps card headers.
    Card bodies were never filtered — exactly matching the report ("sharp
    clarity, issue at the top of the cards"). No RGB-split/chromatic effect
    exists anywhere in the code; this was a side effect, not a feature.
    Fix (v19.3): .inked{filter:none}. Hand-drawn character remains via the
    --r-hand irregular border radii; the decorative chart islands keep
    their own filter rule.
25. **Tide pool invisible in Night Watch.** The pool's sounding dots were
    canvas-drawn in hard-coded light-theme ink (rgba(27,46,75,.55/.7)) on
    the dark chart panel — navy on navy. Fix (v19.3): the draw loop reads
    data-theme each frame; in dark the dots/numbers render in sea-glass
    and pale ink (verified: 4,544 light canvas pixels where ~0 read before).
26. **Specimen cards 01–06 too long on wide monitors.** flex-basis 50vw
    grows without bound (1,024px+ at a 2,048px window) around ~460px of
    content. Fix (v19.3): capped at min(50vw, 820px) on desktop widths.
27. **Studio craft ledger and the ledger panel touch.** FIG. 06 sits inside
    the Arrival pw70 panel flush to its right edge; #ledger-panel (pw50)
    followed with no gap. Fix (v19.3): #ledger-panel gets 3vw left padding
    on desktop (measured gap 57px at 2,048px, 34–41px at laptop widths).
28. **FIG. 00 plate clipped at rest at desktop widths (found during the
    v19.2 pass).** Departure's hero panel was flex-basis 100vw inside a leg
    padded 0 3vw, so the plate's right edge sat exactly 3vw past the
    viewport at the leg's rest stop (measured +45px at 1,500px, +38px at
    1,250px). Fix (v19.3): the panel is 94vw; the plate now rests fully in
    frame at 1,250 / 1,500 / 2,048px.

Also shipped in v19.2/v19.3: opacity floors for the plate's ghost geometry
(compass rose and solstice arc at 0.28–0.48 → 0.52–0.68), the fresh-finish
chart grid alpha doubled, panel-level snap stops so sailing can rest with a
panel framed, and a deeper shadow on the plate. Service-worker cache:
evenfaring-studio-v19.3. Darius's finish is Aged Patina (per his Deck
screenshot) — the earlier pale Daylight shots were the light theme, not the
Fresh finish.

29. **Scroll pull-back / lag when sailing east (Darius, 2026-10-21, after
    v19.3).** The v19.1/v19.2 snap dragged the page BACK to the nearest stop
    whenever he stopped just past one — measured rewind of ~215px after
    stopping 260px past a boundary — and the voyage's 0.14/frame smoothing
    left a long floaty tail after each scroll. Fix (v19.4): the snap is
    directional. A stop ahead in the direction of travel completes
    generously (legs 2.6%, panels 1.8% of the runway); a stop behind only
    settles within 0.7% (~80px, a landing rather than a rewind). Smoothing
    tightened 0.14 → 0.20 with a sooner park cutoff. Verified by scroll
    trails: approach completes forward to the stop; overshoot stays exactly
    where it was left; regression 11/11. Panel-stop cache now also resets
    on resize. Cache: evenfaring-studio-v19.4.

30. **End-of-voyage framing — the FIG. 07 inspector card cut off (Darius,
    2026-10-21, after v19.4: "much smoother, still needs a bit more of a
    tweak towards the end of the page… why is this card cut off").** Three
    stacked causes at the end of the world: (a) the final panel (the
    footer, pw60) begins 37vw from the world's end, so its snap stop sat
    ~730px beyond the maximum scroll — the voyage could never land framed
    on its own ending; (b) the snap's direction came from the last scroll
    event's sign, and trackpad inertia ends in a tiny reverse flick that
    flipped it, so framing near the end often never completed; (c) the
    settle timer was never armed when a gesture's last events landed
    inside the snap lockout, so the page rested unsnapped. Fixes (v19.5):
    a 42vw chart run-out inside the world makes every stop reachable;
    direction is now the dominant direction of the gesture (summed deltas,
    500ms window); the settle timer is always armed and re-checks the
    lockout itself; panel stops are computed from offsetLeft geometry
    (getBoundingClientRect read the mid-lerp transform and poisoned the
    cache); leg/panel stops within 64px merge into one (they had begun
    ping-ponging the settle). Verified Wheel-sailed end-to-end: the
    voyage now lands exactly on the footer stop (scroll 16691/16790,
    footer framed at +14px) and rests still; regression 11/11. The
    inspector also rests framed at its own stop. Cache:
    evenfaring-studio-v19.5.
