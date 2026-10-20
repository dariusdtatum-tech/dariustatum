# Real-World Testing Addendum

Companion to `APP-STORE-2.5.19-CHECKLIST.md` and `APPLE-SUBMISSION-PROCESS-GUIDE.md`
in this folder. Those cover *submission*. This one covers what submission prep
can't catch: how the app behaves in the real world.

Source: Brian Millot's X thread (Oct 19, 2026) asking mobile builders for the one
thing they'd regret not knowing — 164 replies, 99K views. Only 3 replies were
readable without an X sign-in; the advice below is the filtered best of those,
led by the most-liked visible reply, from Iven Prillwitz (@peiper_dev), with one
adaptation flagged as such. Paraphrased, not quoted at length.

---

## The core lesson

Test like reality, not like your desk. An app that only ever ran on a simulator,
on fast office wifi, operated by its own developer, has not really been tested.

## Network conditions

- [ ] **Bad network:** run the full core flow on a throttled / high-latency
  connection (Network Link Conditioner or equivalent). Nothing should hang,
  spin forever, or duplicate an action on retry.
- [ ] **No network:** run the full core flow in airplane mode. For a local-first
  app this is a pass/fail gate: nothing breaks, nothing is lost, and anything
  queued syncs cleanly when connectivity returns.
- [ ] **Network transitions:** start a flow online, drop the connection
  mid-flow, restore it. State must survive all three phases.
  *(Why it matters here: a walking app lives in dead zones, elevators, and
  signal shadows — bad connectivity is its normal environment, not an edge case.)*

## Real devices, real people

- [ ] Test on **physical devices**, not only the simulator — including the
  oldest / slowest device the app supports.
- [ ] Test **payments on a real device** with a sandbox account: purchase,
  restore, and an interrupted purchase. (Pairs with the purchases reverse-check
  in the 2.5.19 checklist.)
- [ ] Put the build in front of a **real user** who has never seen it, watch
  silently, and note where they hesitate.
- [ ] **Get feedback fast:** short loop from build → outsider hands → fix.
  Don't polish in private for weeks what a stranger could falsify in minutes.

## Design baseline

- [ ] Do one deliberate pass against **Apple's Human Interface Guidelines**
  before submission — navigation, touch targets, typography, and system
  behaviors reviewers and users both expect.

## Logging — with an adaptation

Another reply (Tom, @tomvdbogaart) advised logging everything, on the grounds
that data is key. Take the spirit, not the letter:

- [ ] Log **diagnostics and behavior**: crashes, errors, feature usage,
  drop-off points.
- [ ] Do **not** log user content. For a local-first, privacy-sensitive app,
  indiscriminate logging is a liability — it can contradict the App Privacy
  declarations in the submission guide and the trust the app is built on.

---

*If a box here can't be checked, that's the regret this thread was warning
about — find it now, or find it in a 1-star review.*
