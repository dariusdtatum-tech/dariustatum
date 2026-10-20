# Evenfaring Studio Site — Deep Audit Notes (subagent, 2026-10-19)

Working copy: ~/workspace/evenfaring/index.html (433,101 bytes, single page) + style.css (98,658 bytes).
Verified: style.css is NOT linked as the page stylesheet — it is only offered as a downloadable
"modular asset" (line 5031 button, line 9063 downloadTextFileAsset). The page's real CSS is a 3,610-line
inline <style> block, essentially identical to style.css (24-line diff). Editing style.css changes nothing
on the page. The duplicate ships inside the v10 zip as dead weight / confusion risk.

Method: served via python3 http.server on :8099; rendered with Playwright Chromium headless;
screenshots at 1440x900 (light + dark), 390x844 mobile, keyboard tab-through test, plus static
analysis of HTML/CSS/JS and computed contrast ratios. No real-device testing, no field Core Web
Vitals data. Google Fonts failed to load in the sandbox (tunnel blocked), so screenshots show
Georgia/system-ui fallbacks — still representative.

## Reference context folded in
- april-zhu.com lesson: one governing idea, confident quantified claims, cohesive brand.
- Anatomy lesson: the site IS the demo; one governing rule ("the object is the explanation");
  radical honesty (real numbers, Limits section); tokens documented; a11y engineered.
- Urge Walk DESIGN-RULES.md (locked): cream #faf7f1, sand #f1ebe0, navy #24395c, deep sea #3f7a6b
  (text only), sea glass #a9cbc0 (fills only), night #0f1d33/#182b47; Cormorant Garamond italic
  display + Plus Jakarta Sans; 200–350ms soft motion; banned: shadows/glows, blue-purple-pink
  gradients, emoji, urgency badges, red, glassmorphism, centered-everything; copy: sentence case,
  second person, zero exclamation marks, banned-words list; sounds OFF by default + Still scenes
  follows Reduce Motion; tap targets >=44pt; contrast >=4.5:1/3:1.

## HEADLINE FINDINGS (verified)

1. **The token ledger documents the wrong tokens.** The on-page "FIG. 08 · Formal Design System &
   Patina Specimen Ledger" claims: --bg #faf7f1/#0f1d33, --teal #216862/#a9cbc0, --timber
   #b5673f/#df8d62, with contrast "12.6:1 / 14.8:1" and "6.4:1 / 10.1:1". The actual code tokens are:
   --bg #f6f1e6/#0f1d33, --teal #1e635d/#a9cbc0, --timber #ae5f37/#df8d62. Real computed ratios:
   --text 12.11:1 / 14.49:1, --text-2 7.06:1 / 10.24:1. The ledger matches neither the code nor the
   locked Urge Walk palette. Three palettes, zero agreement — a direct violation of the Anatomy
   honesty lesson, on the page whose job is to certify honesty.

2. **Drift from the locked brand.** Locked: cream #faf7f1, navy #24395c, deep sea #3f7a6b. Site uses
   #f6f1e6, #1b2e4b, #1e635d. Timber orange #ae5f37/#df8d62 is not in the locked palette at all.
   Dark mode (#0f1d33 bg) matches locked night. Decision needed: re-align to locked tokens, or
   formally amend the locked system (DESIGN-RULES.md format) to admit timber as a "beacon" token.

3. **Timber text fails WCAG AA.** #ae5f37 on #f6f1e6 = 4.15:1, used as text color at 11.5px
   (--text-xs, e.g. telemetry spans). Needs 4.5:1 for normal text. (Dark timber #df8d62 on #0f1d33
   = 6.53:1, passes.) Fix: darken light timber to ~#9a5230 or restrict timber to large/graphic use.

4. **Ambient audio starts on first keypress.** bindFirstGestureAudioUnlock() binds pointerdown,
   keydown, touchstart, etc.; the first Tab press starts the Web Audio harbor soundscape
   (default soundEnabled=true, "Harbor Audio: On"). Screen-reader users get ocean audio competing
   with speech on their first navigation keystroke. The app's own rule is sounds OFF by default
   with explicit opt-in — the studio site inverts it. No <audio> autoplay (AudioContext needs
   gesture), and a toggle exists, so WCAG 1.4.2 technically passes, but the UX is hostile.

5. **PWA claims are false right now.** The page registers ./sw.js which 404s (console error
   verified); no manifest link found. "Installable on iOS & Android, resilient offline" is not
   true until sw.js + manifest ship. Also the FIG. 04 embed iframes
   https://evenfaring.com/evenfaring_studio_folio_v9.html?embed=1 — external dependency, fails
   offline/local.

6. **Accessibility: evident care, real gaps.** Positives: skip link (first tab stop, "Skip to
   content"), logical tab order, visible :focus-visible ring (verified in screenshot), 31
   tabindex=0 + role=button SVG nodes with keyboard handlers and aria-labels, landmarks
   (header/nav/main/footer), 8 aria-live regions, reduced-motion kill-switch
   (`*,*::before,*::after{animation:none!important;transition:none!important}`), no canvas/rAF.
   Gaps: 5 h1s (hero + Communities/About/Contact/Privacy overlay views); heading skips
   (h1→h4 "Solar Watch & Urge Walk 5-Minute Wave", h1→h3 "Useful features stay free"); 18 of 22
   SVGs lack aria-hidden/role (SR noise); role=tablist with 3 role=tab but 0 tabpanel and no
   arrow-key support (de-role to buttons or implement full pattern); full-screen workbench
   dialog has role=dialog aria-modal=true but no focus trap found in JS; `forced-color-adjust:none`
   on themed roots disables Windows High Contrast; emoji used as icon glyphs (⚓🌊📐🧭⚙🔔 etc.,
   71 occurrences) — banned by house rules and announced by screen readers ("⚙ Deck" toggle).

7. **IA sprawl.** 14 figures (FIG. 00–13, plus 00A/00B) mixing four jobs: studio marketing, live
   product demos, dev/PWA tooling, print shop. Nav exposes only Apps/Communities/About/Contact;
   ~10 figures reachable only by scrolling. Header chrome: telemetry ribbon (2 dense lines of
   insider jargon at 11.5px) + logo + Deck pill + 4 nav + Harbor Audio pill + 3-way theme
   segmented control. Mobile: ribbon wraps to 3 lines, header becomes 3 rows, bottom dock
   (Desk/Urge Walk/Dayspan/Tabletop/Controls) overlaps body copy (pull quote runs under it).

8. **The 5-second test passes on "what", strains on "what next".** H1 "Small, steady apps, made
   with care." + lede ("one-person studio drafting quiet, durable instruments for everyday
   life… without noise, ad trackers, or streak guilt") + 2 CTAs = clear. But the hero's right
   column is a working tidal instrument (time slider, 5 presets, LINKED TO APPS toggle, BUS SYNC,
   M2 soundings, X-ray formula) — thrilling, but a first-time visitor must learn the folio
   metaphor (FIG.? bus sync? 5 bells?) before acting.

9. **Motion: alive but mostly decorative.** 10 keyframes: settle-in, sun-breathe, wave-bar-pulse,
   buoy-bob, beacon-occult, cloud-drift, seaglass-breathe, star-twinkle, steam-rise (+transitions).
   Functional: sun-breathe in breath mode (guides the exercise), settle-in entrances, the
   time-of-day slider reshaping the apps (the site's best "alive" moment — motion that
   communicates). Decorative: star-twinkle, cloud-drift, steam-rise, buoy-bob — slow/subtle/
   reduced-motion-gated, so low-risk, and they carry the cartography personality. No
   IntersectionObserver scroll reveals — the page can feel static below the hero.

10. **Copy voice is excellent.** 0 exclamation marks in visible copy; sentence case; second person;
    no banned words in copy ("unlock" hits were JS-only). Standouts: "No streaks to protect—just
    the next small step." / "A good chart never shouts at the navigator for yesterday's weather."
    Breaks: telemetry chrome is engineer-jargon ("M2 HARMONIC SOUNDINGS (FM)", "BUS SYNC → 01·02·03",
    "⌖ X-RAY MECHANISM: h(t) = H₀ + A·cos(ω_M2·t)…") — the calm voice stops at the instruments'
    labels.

11. **Performance.** 433KB single HTML, 0 <img>, fonts non-blocking (preconnect + media=print
    onload + display=swap), end-of-body inline script (3,724 lines, ~150KB — parser-blocking but
    after content). No render-blocking CSS/JS. LCP likely fast (text + inline SVG). Risks: font-swap
    CLS (mitigated by metric-compatible fallbacks Georgia/system-ui), 3.7k-line JS main-thread
    cost on low-end phones, sw.js 404 console error, external iframe dependency. No field data.

12. **Version hygiene.** <title>/og:title/twitter:title still say "v9.0"; the file is v10.
    robots noindex present (preview flag — must be removed at launch).

## Contrast table (computed, sRGB)
Light: text 12.11 | text-2 7.06 | text-muted 4.69 | teal-on-bg 6.22 | timber-on-bg 4.15 FAIL |
on-accent-on-accent 10.27 | chart-sub-on-chart-bg 5.48 | chart-ink-on-chart-panel 11.81
Dark: text 14.49 | text-2 10.24 | text-muted 6.11 | accent-on-bg 9.64 | timber-on-bg 6.53 |
chart-sub-on-chart-bg 8.11 | chart-ink-on-chart-panel 13.52 — all pass.

## Proposed locked token direction (for the PDF's written design system)
Base on Urge Walk DESIGN-RULES.md: cream #faf7f1 / sand #f1ebe0 / navy #24395c / deep sea #3f7a6b
(text-only) / sea glass #a9cbc0 (fills-only); night #0f1d33 / #182b47. Resolve timber by formal
amendment or removal. Type: Cormorant Garamond (display, italic moments) + Plus Jakarta Sans;
8-step scale exists — keep, but raise telemetry minimum or restrict micro-labels to graphics.
Spacing 4/8 grid, radii 8/10/14, elevation = hairlines only (no shadows per ban). Motion:
200–350ms ease-out-quart; ambient loops ≤10s, subtle, always reduced-motion-gated; sounds off
by default. Banned list mirrors the app's (emoji icons, decorative shadow/glow, gradients,
urgency badges, centered-everything). Ship checklist: both themes, contrast ≥4.5:1, keyboard
walk, no emoji, sounds opt-in, version strings current.

## Top 6 fixes (in order)
1. Make the specimen ledger truthful: document actual code tokens (or re-align code to locked
   palette, then document). The certifying page must not lie.
2. Resolve timber: darken to pass AA as text or remove from text use; formally lock or drop it.
3. Ambient audio default OFF; never start on keydown — match the app's own sound rule.
4. Ship sw.js + web manifest, or remove "installable / resilient offline" claims.
5. A11y pass: single h1, fix heading skips, aria-hidden on decorative SVGs, complete or
   de-role the tab pattern, focus trap for workbench dialog, drop forced-color-adjust:none,
   replace emoji icons with inline SVG.
6. IA triage: anchor-link every figure (or add a figure index), collapse the telemetry ribbon
   on mobile, fix dock overlap with safe-area padding.

## Alive vs calm (guidance for the report close)
"Alive" already works where motion = the product: the solar watch reshaping the apps, the
urge-wave scrubber, breath-mode sun. Keep ambient loops (buoy, beacon, clouds, stars) — they
are slow, subtle, and gated — but promote them from decoration to signal where cheap (beacon
occults only when its instrument is live; tide lines already respond to the slider). Do NOT
add scroll-jacking, parallax, or entrance choreography; the calm is the differentiator, and
the Urge Walk "Still scenes" pattern (one setting that stills everything, beyond the OS
preference) is the model to copy. New "alive" should be: instruments that respond, not pages
that perform.
