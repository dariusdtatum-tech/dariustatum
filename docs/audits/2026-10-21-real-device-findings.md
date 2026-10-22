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

31. **v20 "THE LIVING CHART" (Darius's call, 2026-10-21: "Take 10 ideas
    that would work and blend well and use them" — from the Charted Waters
    25-site study).** The ten, chosen to read as one release: (1)
    curiosities at honest soundings — serpent (40 FM), whale (55 FM),
    wreck of the good ship Distraction (80 FM), a fellow ship homeward
    bound — line glyphs with one-line captions in the chart's empty water;
    (2) marginalia in the Open Sea ("Most of the sea is just sea." /
    "Still water. The captain is patient."); (3) named waters printed at
    each leg (The Sheltered Sound / The Long Swell / The Harbor Reaches /
    Home Water); (4) sheet tags at the leg joins (Sheet I–IV); (5) a
    sea-state instrument — the vessel readout now carries SHELTERED /
    LONG SWELL / HARBOR CHOP / GLASSY by leg; (6) chart of the day — one
    curiosity inked darkest by date seed, captioned "sounded today";
    (7) chart-layer toggles in the Deck (Route / Soundings / Notes),
    persisted on-device; (8) catalog line + charting stage on every
    specimen (Charted & Corrected / Charted / Sounded / Sketch ·
    "Charted by the studio · Scale 1:1 · First sounded 2026"); (9)
    Notices to Mariners in the ledger panel (№№ 1–4, the real v19.3–19.5
    corrections in bulletin language); (10) Fair wind — a hands-free
    ~75-second sailing of the whole chart, cancelled by any input, becalmed
    under reduced-motion. Build notes: three fair-wind fights were found
    and fixed in the harness — the snap now stands down while it blows
    (window.__fairWind), CSS smooth scrolling is suspended while sailing
    (it animated every scrollBy into a stall), and the step clock tolerates
    throttled frames. Decorations sit below the fixed header's band and are
    hidden in the stacked mobile layout. Regression 11/11; toggles persist
    across reload; zero page errors. Cache: evenfaring-studio-v20.

32. **FIG. 07 still cut + footer placement (Darius, 2026-10-21, on v20).**
    Two reports: the footer block should sit in the bottom right ("not
    the another captain but the other stuff"), and the storage inspector
    "is still cut off." The cut had TWO remaining causes, one of them a
    correction to items 24–30: (a) approached from the east on a
    backtrack, the inspector's stop fell outside the snap's 0.7%
    behind-limit, so it rested ~215px short — landing zones widened
    (panels settle from 1.6% behind, legs 1.2%; the ~500px rewinds of
    item 29 stay dead); (b) THE persistent one — the inspector section
    carries Gemini's snd-drift-l offset, translateX(−56px), which slides
    its left ~55px under its panel's clip edge (the panel's overflow-y
    forces overflow-x clipping). "On-device…" → "…vice", "TRACKING" →
    "…CKING" at every scroll position, in every screenshot since v19.3:
    an internal clip, not a framing one. The scroll fixes were necessary
    but never sufficient. The inspector is now exempt from the drift
    (#fig-privacy{transform:none !important}); verified in-frame: the
    full header and all three cells read complete, approached from
    either direction. The footer panel now rests at the bottom of the
    Arrival leg (align-self:flex-end, 9vh margin); the fellow ship is
    untouched. The vessel readout also fades over the final approach
    (p>0.986), symmetric with the FIG. 00 fade at departure. Cache:
    evenfaring-studio-v20.2.

33. **Arrival spacing + the end should feel like the end (Darius,
    2026-10-21, on v20.2).** Three points: the inspector and the
    "Write to the studio" panel crowded each other (contact copy began
    at the card's edge); the footer landing left the right of the screen
    as empty run-out; and the end should feel like the end — the
    chart-index block seen AT the end, not before it. Fix (desktop
    voyage only, min-width:861px): the contact panel gains a 6vw left
    margin (measured gap card→heading: 114px); the footer panel widens
    pw60 → 100vw with its block anchored bottom-right (margin-left:auto);
    the run-out shrinks 42vw → 8vw, which makes the footer stop coincide
    with maximum scroll (16,810 = 16,810 at 2048px) — the landing IS the
    end of the chart, and the readout's final-approach fade leaves the
    frame clean. Panel stops are offsetLeft-derived, so all landings
    recomputed themselves. Regression 11/11. Cache:
    evenfaring-studio-v20.3.

34. **v21 THE HARBOR WORKS (Darius, 2026-10-21).** His call: specimens
    04–06 were the three apps repeated in workbench dress — filler.
    Brainstormed from the Charted Waters study; his brief: "Do what you
    think is best, and what will blend well with the foundation." The
    trio with a story — touch, play, memory: SPECIMEN 04 is now The
    Chronometer, Opened (study #16: a peek-inside slider fading case →
    dial → gear train, and a wind control — she runs down over ~40s
    unless rewound, state reported in the instrument voice); 05 is Rig
    the Route (#18: tap four marks to lay your own course on a blank
    chart patch, scored live in fathoms against the charted course,
    with a Sail-it run); 06 is The Chart Drawer (#14: three rolled
    charts — the v14 Parallax Chart, the Gemini Fork, the Fresh Studio
    finish — unrolled by accordion, each with its honest striking note).
    The leg is renamed LEG 3 · THE HARBOR WORKS with matching copy. The
    workbenches themselves are untouched and still open full-screen from
    specimens 01–03. Build notes: the exhibit wrapper had to escape
    .card-live's flex row (display:block) or the SVG collapsed to
    intrinsic size; gear train sits inside the dial's r57 so the opened
    movement reads as one instrument. Regression 11/11; exhibits probed
    (peek cross-fade, wind state, course math 143 vs 112 FM on A-C-D-B,
    boat sails, drawer accordion); workbench modal verified opening from
    card 01. Cache: evenfaring-studio-v21.

35. **v22 THE THREE-ZONE HEADER (Darius, 2026-10-21).** His brief, with
    April Zhu's bar as the study: the header huddled inside the 1080px
    centered .wrap — on his wide screen everything sat in the middle,
    stacked in rows. "Evenfaring should be to the left, some other
    stuff to the right, some in the middle." The bar now spans the
    viewport in three zones (new layer #v22-three-zone-header; the leg
    nav moved out of .header-controls to be a direct child of .wrap so
    the grid can place it): brand left, leg nav centered (nowrap — it
    can never stack vertically again), instruments + Deck right. At
    >=2100px it is one line; in the 861–2099px laptop band the
    instruments take a full-width second row, right-aligned — the bar
    is SHORTER than v21's three rows at 1440px, and the hero headline
    ("Small, steady") is no longer clipped under the header there (a
    pre-existing v21 overlap this happens to cure). The <=860px phone
    stack is untouched except flex order, which preserves its old
    sequence (brand, Deck, instruments, nav). Audio untouched; patina
    default untouched. Regression 11/11 (first run's readout failure
    was a cold-Chromium flake — baseline v21 fetched from GitHub and
    re-run for comparison; warm runs pass 11/11 on both). Cache:
    evenfaring-studio-v22.

## 42. 2026-10-21 (late) — REAL-DEVICE VERDICT: MOBILE ACCEPTED; resume scoped to desktop (v29)

- Darius's phone verdict on the v19→v28 mobile build, with screenshots and a full screen recording: "The mobile version is 100x better than it was this morning... the mobile is clean." Solar Meridian & Tidal Chart functional; tide pool decently functional; Specimens 01–03 "smooth and clean"; the recording also shows the v23 chronometer winding/peeling/labels, the v24 chart room panning with correct island names, the v26 Appearance sheet, the ledger drawer with live patina flip, and the v27 ship's log + colophon all working on his iPhone. **The phone re-verdict that has gated the mobile build since item 21 is now given: ACCEPTED.** Desktop check deferred to tomorrow by Darius.
- One defect reported: on load, the site did not show its beginning — the opening text was already scrolled past (he had to scroll and hold to screenshot it). Root cause: rec 9's resume (item 40) restored his saved scroll position in the stacked mobile layout too. On the desktop voyage resume is a feature; in the mobile stack a mid-page landing reads as a bug.
- v29 fix (site commit pending below): the v27 module returns early when `dataset.voyageMode === 'stacked'` — no recording, no restore; mobile always begins at the beginning. Cache `evenfaring-studio-v29`.
- Verified: verify-v29.py 5/5 (mobile emulation with a seeded saved position: scrollY stays 0, hero headline on screen; desktop: resume still restores exactly); harness 11/11.

## 43. 2026-10-21 (late) — v29 incomplete: Safari's own scroll restoration (v30)

- Darius re-checked and reported the beginning-text issue "still there," with a second screen recording. Two causes: (1) the recording begins from the iOS-restored tab — the old page state, no reload ever ran; (2) a genuine miss in v29: the early return skipped the whole resume block, including the line that set `history.scrollRestoration = 'manual'` — so Safari's native scroll restoration stayed active and put him back mid-page on any real reload. Item 42's fix was correct about the site's own code and incomplete about the browser's.
- v30 fix (site commit pending below): in stacked mode the module now sets `history.scrollRestoration = 'manual'` AND forces an instant scroll to the top on load (immediately and again at 350ms, excepting #workbench hashes), then returns — no recording, no restore. Cache `evenfaring-studio-v30`.
- Verified: verify-v30.py 7/7 — the suite now includes the reload case v29's probes missed (scroll to 4500, reload, lands at 0 with the hero headline on screen); desktop resume still restores exactly. Harness 11/11.
- Note for the record: Darius's phone needs one manual refresh (or tab close/reopen) to pick up v30, because the currently open tab predates it.

## 44. 2026-10-21 (late) — THE REAL CAUSE: the header covered the hero all along (v31)

- Darius, after v30: "It's just not working. It's there but it won't start where it needs to." A clean frame extracted from his recording shows the hero paragraph's first line sliced mid-glyph by the header's bottom edge — text running UNDER the header, not a scroll offset.
- Measured in emulation (mobile, scrollY = 0): `.voyage-stage>header.site` is `position:absolute`, 255px tall closed / 376px open; the hero eyebrow sits at y 59–97 and the H1 at y 109–231 — entirely inside the header's box, hit-tests returning header buttons. **The beginning text was never scrolled away; it was rendered underneath the absolutely-positioned header**, which is the stage's LAST DOM child and only ever reached the top via absolute positioning. Items 42/43's scroll-restoration theories were wrong — they treated a symptom picture (tab state) instead of measuring the layout; the v29/v30 changes are harmless and stay (desktop resume unchanged), but they were not the fix.
- v31 fix: (1) CSS, ≤860px only — `.voyage-stage{overflow:visible}` and the in-stage header becomes `position:sticky; top:0` (in-flow); (2) in the sail engine's stacked branch, the header node is moved to the front of the stage (`insertBefore(header, stage.firstChild)`) so the flow starts with it. Drawer opening now pushes content down instead of covering more of it; the header stays pinned while scrolling, as before.
- Verified while the VM's Chromium was healthy: verify-v31.py 8/8 (hit-tests: eyebrow and headline return themselves; headline at y364 below the 255px header; drawer-open: headline pushed to 485, uncovered; header pinned at top=0 after scrolling 1600; desktop header still absolute). Same session: harness 11/11, v30 7/7, v24 15/16 (standing environmental miss). The v23 chrono suite (desktop-only viewports; this change is media-gated to ≤860px and a stacked-only branch, so it cannot execute there) could not be re-run afterward — the VM's Chromium began dying at launch (SIGTRAP, ~219MB free RAM); recorded here as an environment failure, not a site result.
- Lesson filed: on a "content missing at the top" report, measure element boxes and hit-tests before theorizing about scroll state.

## 45. 2026-10-21 (late) — v31 CONFIRMED on Darius's iPhone

- Darius re-checked after the v31 refresh: screenshot shows the header, then directly below it the eyebrow and the full headline "Small, steady apps, made with care." — the beginning is the beginning. Verdict: "Great work." A follow-up recording tours the full mobile site working (Solar Meridian scrubbing, tide pool, specimens, chronometer wind/peel, ledger stations, storage inspector, ship's log). The hidden-beginning saga (items 42–44) is closed on the only verdict that counts: his device.
