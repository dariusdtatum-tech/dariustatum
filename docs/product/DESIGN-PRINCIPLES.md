# Design principles: dariustatum.com

Set by Darius on 2026-11-09, replacing the fixed creative direction used for the
first ten improvement rounds (PR #1).

## What stands

- **No copying.** References are studied for craft and translated, never reproduced.
- **No impersonation.** Nothing presents as another person, studio or brand.
- **Sophisticated elegance.** Calm, restrained, considered.
- **Creative.** The site is a piece of craft, not a container for one.
- **Anti-slop, no AI slop.** No generic template moves; AI output is sketch material,
  never shipped as-is.
- **Give everything to the icons and the designs.** Every mark and drawing gets full care.

## What is no longer fixed

- **The compass mark is not a rule.** It fit the earlier approach and the current one,
  but if a better focus appears, take it. Darius: "there seems to always be a better focus".
- The lighthouse hero, the verse and the cartography language are the current direction,
  not locked. They are judged against the principles above.

## Standing project facts (unchanged)

- Real photography; Pixabay is the house source; no people in visuals; commercially
  usable assets only; nothing from Adobe.
- `noindex, nofollow` until launch; contact stays the `hello@example.com` placeholder.

## How direction is chosen now

The ten open-ended rounds showed that "be creative" alone leads nowhere. Direction comes
first, then rounds against it:

1. Reference board: personal studio sites marked Keep / Not me
   (claude.ai artifact "Studio Reference Board", private to Darius).
   Batch 1 keeps: Bret Victor, Andy Matuschak, Hundred Rabbits, Olafur Eliasson,
   Rauno Freiberg, Hiroshi Sugimoto, Tyler Hobbs.
2. A one-page direction brief from the keeps, Urge Walk's language and his current focus.
3. Two or three directions designed as frames (Figma), compared, one chosen.
4. Build and rounds in code against the chosen design.

## Tools

- Pixabay API key lives in `.env` (`PIXABAY_API_KEY`), which is git-ignored. Never commit it.
