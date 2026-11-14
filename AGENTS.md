# Agent notes (dariustatum.com)

Read `docs/product/DESIGN-PRINCIPLES.md` first; it is the source of truth for this site and overrides defaults. The README explains how to run the site and what the files are.

This is a plain static GitHub Pages site. There is no build step. Do not change the service worker cache name (`dariustatum-vNN` in `sw.js`) unless a deploy actually changes cached site files. Tooling-only changes do not need a bump.

## Before you open a PR

These steps sit alongside the design principles; they do not replace them.

1. Look up current HTML, CSS and JavaScript patterns in Chrome's Modern Web Guidance before writing new front-end code:
   - Search: `npx modern-web-guidance@latest search <topic>`
   - Read a guide: `npx modern-web-guidance@latest retrieve <guide-id>`
   - Repo: https://github.com/googlechrome/modern-web-guidance
2. Before using any newer platform feature, confirm it works in iPhone Safari (caniuse.com or MDN), or add a fallback. iPhone Safari comes first.
3. Run the quality check from the repo root and paste its summary into the PR:

   ```bash
   npm install
   npm run check
   ```

   `./tools/check.sh` does the same, and runs `npm install` on the first use. The check is local and free. Do not add a GitHub Action for it while the Actions minutes limit is in effect (until 1 December 2026). The Modern Web Guidance scan is advice; the check fails on axe violations, a Lighthouse accessibility score under 95, broken internal links or assets, console errors, horizontal overflow at 390px, or a personal Google mail address in tracked files.
4. One editor per repo at a time. Use single-purpose commits. Never merge your own PR.
