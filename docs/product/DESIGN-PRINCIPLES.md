# Design principles: dariustatum.com

Set by Darius on 2026-11-09, replacing the fixed creative direction used for the
first ten improvement rounds (PR #1).

## What stands

- **No copying.** References are studied for craft and translated, never reproduced.
- **No impersonation.** Nothing presents as another person, studio or brand.
- **Sophisticated elegance.** Calm, restrained, considered.
- **Creative.** The site is a piece of craft, not a container for one.
- **Anti-slop, no AI slop.** No generic template moves. AI slop is what looks or sounds like AI
  (Darius, 2026-11-09): AI work that respects the visuals and feels authentic is valued, not slop.
- **Give everything to the icons and the designs.** Every mark and drawing gets full care.
- **How can we evolve further?** (Darius, 2026-11-09.) Every time a direction works, ask
  what the next step in its evolution is, instead of polishing the current one. Motion is
  the first answer: a still that becomes a living scene, nature that moves (sea, grass,
  branches in the wind), always calm, never decoration.

## What is no longer fixed

- **The compass mark is not a rule.** It fit the earlier approach and the current one,
  but if a better focus appears, take it. Darius: "there seems to always be a better focus".
- Nothing from earlier versions is a standing design: not the lighthouse, the verse, the
  cartography language or the palette. The next direction may be completely new.

## The focus (Darius, 2026-11-09)

"Overcoming the hardest period of one's life and choosing a totally different career …
that kind of expression of being into a different zone and mentality, i want to see that."
Open questions he put on the table: larger than life or minimal, or both; something few
have done or something many have done; thinking outside the box; nothing like earlier
versions; completely anew.

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

## Chosen path (2026-11-09)

Two prototypes were built in `prototypes/`: Long Exposure (press and hold until a restless
photograph settles) and The Rebuild (24 numbered parts fit into place as you scroll).
Darius: "Both speak to me but Long Exposure is the better path." Long Exposure is the
direction; The Rebuild stays as a reference.

## Premium, with the tools we have (Darius, 2026-11-09)

- No location shoot and no new paid library until the work earns money: stay on free sources
  and existing subscriptions (Pixabay, Unsplash+, ZapSplat).
- Reference for the feeling: Portal (free, yet premium through motion and smooth transitions).
- Stop designing around older phones. Motion and 4K are the default; 4K sources wherever
  Pixabay has them. Reduce Motion stays (an accessibility setting, not an old-phone fallback).
- The Urge Walk app's motion is built in the Expo app until the Mac mini arrives.
- Homepage: Long Exposure replaced the chart site (v70). The old page is in git history.

## Working agreement (Darius, 2026-11-09)

"Yeah merge any small verified site fixes then." Small, verified fixes to the websites are merged by
Claude without asking, then reported. Larger changes (a new direction, a new page) still wait for "merge".

## Pixabay licence, as read on 2026-11-09 (not legal advice)

Source: pixabay.com/service/terms (section 5) and pixabay.com/api/docs.
- Free for commercial and non-commercial use, worldwide, perpetual; credit not required.
- Modifying and combining into a new work is allowed.
- Not allowed: selling or giving away the content "on a Standalone basis" (as-is, or only
  filtered, cropped or resized), including wallpapers, prints and merchandise; using content
  showing trademarks, logos or brands commercially; implying endorsement; using it in a
  trademark or business name; claiming you made it.
- Pixabay does not warrant model or property releases ("We do not warrant that any consents
  or licenses have been obtained"); that risk stays with us. Hence: no people, no
  recognisable private buildings, no visible brands.
- Music and sound effects fall under the same licence.
- API: download images to our own server (no permanent hotlinking), cache requests 24 h,
  no systematic mass downloads, show the source when displaying search results.
- German law, Berlin jurisdiction.

## Tools

- Pixabay API key lives in `.env` (`PIXABAY_API_KEY`), which is git-ignored. Never commit it.
