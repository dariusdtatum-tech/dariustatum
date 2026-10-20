# App Store Pre-Submission Checklist — Guideline 2.5.19

**Purpose:** No hidden, dormant, or undocumented features in the submitted binary.
App Review must be able to see and understand everything the app can do. Features
hidden behind undocumented switches, server toggles, or test code risk rejection —
and, in Apple's wording, removal and developer-account termination.

Run every item below on the **Release / production build** (not a dev build)
before uploading to App Store Connect.

Source: Community fixes compiled by Luka Shaw (@vovalukashov) on X, Oct 20, 2026,
summarizing 50+ replies to his post on rule 2.5.19. Adapted here for a
React Native / Expo app, with the original Swift notes kept where they apply.

---

## 1. Test switches and debug code

- [ ] Every test/debug switch is gated so it cannot ship:
  - React Native / JS: guarded by `__DEV__` or a build-time env flag that is
    **false/absent** in the production EAS build profile.
  - Native Swift (if any): wrapped in `#if DEBUG` so it never compiles into Release.
- [ ] Grep the production bundle / built app for every switch and flag name:
  - `grep -r "<flag-or-switch-name>" <built-app-or-bundle-path>`
  - Repeat for each name on the flag inventory (see §2).
- [ ] No debug menus, dev panels, or gesture-activated hidden screens are
  reachable in the Release build.

## 2. Flag inventory + CI guardrail

- [ ] Maintain a denylist file of all feature-flag / test-switch names.
- [ ] CI runs a check against that denylist on the Release build and **fails the
  build** if any name is found.
- [ ] The check also covers flags pulled in from shared packages / dependencies,
  not just first-party code.

## 3. Purchases

- [ ] No purchases mock ships in the binary. Mocks are for local tests only and
  are excluded from the production build.
- [ ] Simulator/UI-test purchases use a non-shipping setup:
  - Swift: a `.storekit` file in the scheme.
  - React Native: the sandbox / test-store configuration injected at test time,
    not bundled as a runtime toggle.
- [ ] **Reverse check:** the real purchases implementation *is* present in the
  Release binary. Confirm a real (sandbox) purchase completes on a Release-config
  build — this catches a gate that removed the wrong half.
- [ ] Run at least one UI test against the **Release configuration** before
  submitting; this exercises the checks above automatically.

## 4. Remote config and server-controlled features

- [ ] List everything that can be turned on/off or changed from a server
  (remote config, feature flags, price tests).
- [ ] Price/A-B tests via remote config: disclosed in review notes (see §6).
- [ ] No feature can be enabled remotely that App Review has not seen.
  If it isn't visible to the reviewer, it doesn't ship in this version.

## 5. Reviewer access

- [ ] Provide a demo login with **every feature enabled**.
- [ ] The account works for the full review period (doesn't expire, isn't
  rate-limited, doesn't require personal SMS/email codes).
- [ ] Any feature that normally unlocks over time, by location, or by payment
  is already unlocked for the reviewer account.

## 6. Review notes (copy, paste, complete)

Add to App Store Connect → App Review Information → Notes:

```text
Features behind flags / remote config:
- [flag name]: [what it does] — [how and when it will be enabled]

Server-controlled behavior in this build:
- [e.g. price test / remote config keys and what they change]

Demo account:
- Username: [...]
- Password: [...]
- This account has all features enabled.

Anything not listed here is not present in this binary.
```

- [ ] Notes list every flagged feature and its planned rollout.
- [ ] Notes match reality — nothing enabled later that wasn't described here.

## 7. Final pass

- [ ] Fresh production build (not a reused dev/TestFlight-internal artifact).
- [ ] Installed the Release build on a real device and walked the full app.
- [ ] Searched the binary/bundle for the full denylist one last time.
- [ ] Review notes completed (§6) and demo login tested from a logged-out state.
- [ ] Version/build in App Store Connect matches the build you just checked.

---

*If any box can't be checked, don't submit yet — fix it or disclose it in the
review notes. Prevention beats an appeal.*
