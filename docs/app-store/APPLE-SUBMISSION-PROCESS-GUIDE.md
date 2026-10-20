# Apple's App Store Submission Process — The Full Walkthrough

For a first-time iOS submission of a React Native / Expo app. Written to sit next to the narrower `APP-STORE-2.5.19-CHECKLIST.md` in this folder — that file covers one guideline (2.5.19, hidden features); this one covers the whole road from enrollment to release, and points back to it where it fits.

**How to read the tags:** **[Required]** = Apple requires it for every app. **[Conditional]** = only applies if the condition is true for your app. **[Recommendation]** = not required, but reduces rejection risk or friction.

Facts are cited inline. Everything was verified against Apple's and Expo's own pages on 2026-10-20 (full source list at the end), except where a gap is explicitly flagged.

---

## 1. Prerequisites — before you can submit anything

### 1.1 Enroll in the Apple Developer Program — [Required]
You cannot submit to the App Store without a paid membership: **99 USD per membership year**. ([developer.apple.com/programs/enroll](https://developer.apple.com/programs/enroll/))

- **Individual / sole proprietor:** enroll with your own Apple Account (two-factor authentication on). Use your **legal name** in the Apple Account — putting an alias, nickname, or company name in the first/last name fields delays approval. Apple confirms your legal name, and that name is displayed as the **seller name** on the App Store.
- **Organization:** also possible, but heavier. The person enrolling must be the Account Holder with legal authority to bind the org (owner/founder, executive, senior project lead, or an employee granted that authority). The org must be a real legal entity — no DBAs, trade names, or branch offices. You need a **D-U-N-S Number** (free from Dun & Bradstreet) so Apple can verify the org's identity and address, a work email on the org's domain, and a **publicly available, functional website** on the org's domain (a social media page or a parked/minimal page is not accepted). The org's legal name becomes the seller name.
- **[Recommendation]** For a one-person studio shipping its first app, **Individual** is the normal path. The trade-off to know up front: your legal personal name shows as the seller name on your product page. Organization enrollment hides that behind the company name but adds the D-U-N-S wait and website requirement. (Urge Walk note: guideline 5.1.1(ix) says apps in highly regulated fields — banking, healthcare, gambling, legal cannabis, air travel, crypto exchanges — "should be submitted by a legal entity that provides those services rather than an individual developer." A self-help/recovery support app is not automatically in that list, but if the app ever makes clinical/treatment claims, this starts to matter — see §9.)

### 1.2 Agreements, tax, and banking — [Required for paid apps/IAP; partly for free apps]
In App Store Connect this lives under **Business → Agreements** (older guides call the whole area "Agreements, Tax, and Banking"). ([developer.apple.com/help/app-store-connect/manage-agreements/sign-and-update-agreements](https://developer.apple.com/help/app-store-connect/manage-agreements/sign-and-update-agreements))

- **Free apps** are distributed under the Apple Developer Program License Agreement — no Paid Apps Agreement needed.
- **[Conditional — selling the app or any In-App Purchase]** The **Account Holder** (only that role) must sign the **Paid Apps Agreement** in Business → Agreements tab → Paid Apps → View and Agree to Terms. It must be **active** to submit or update paid apps and IAPs, and when Apple issues a new version of it, you can't create a new app or IAP until the Account Holder accepts it. You also can't even create an app record until the Account Holder has signed the latest agreement (see §2).
- **Tax forms — [Conditional: Paid Apps Agreement signed].** All developers must complete a US tax form under the Paid Apps Agreement; US-based developers get the **W-9** (SSN for an individual, EIN for a business entity). Non-US developers get routed to W-8BEN / W-8BEN-E / W-8ECI as applicable. Entered via Business → Agreements tab → Tax Forms → Add Tax Info. ([developer.apple.com/help/app-store-connect/manage-tax-information/provide-tax-information](https://developer.apple.com/help/app-store-connect/manage-tax-information/provide-tax-information))
- **Banking — [Conditional: you want to receive money].** Also under Business → Agreements tab → Bank Accounts → Add Bank Account. Two gotchas straight from Apple's page: you must sign the Paid Apps Agreement **before** banking can be added, and **all required tax forms must be submitted before Apple will process banking information**. Account Holder/Admin/Finance roles only. ([developer.apple.com/help/app-store-connect/manage-banking-information/enter-banking-information](https://developer.apple.com/help/app-store-connect/manage-banking-information/enter-banking-information))
- **[Recommendation]** If there's any chance of IAP later, do Paid Apps + W-9 + banking before the first submission anyway — a lapsed or unsigned agreement blocks updates, not just new paid content.

### 1.3 Signing credentials: certificates & provisioning — [Required, but EAS can own it]
Every App Store build must be signed with an Apple distribution certificate and a provisioning profile tied to your bundle ID.

- **[Recommendation — the Expo default]** Let **EAS manage credentials**. EAS Build "can generate and manage your provisioning profiles and distribution certificates for you," or use credentials you provide. EAS Submit works on macOS, Linux, or Windows — **no Mac required** for the whole pipeline. ([docs.expo.dev/build/introduction](https://docs.expo.dev/build/introduction/), [docs.expo.dev/submit/ios](https://docs.expo.dev/submit/ios/))
- **[Conditional — manual path]** If you manage credentials yourself (Xcode / Apple Developer portal), you own certificate expiry and profile regeneration. With EAS managed credentials this is Expo's problem, which is the point.

---

## 2. Create the app record in App Store Connect

Do this **before** uploading a build. Required role: Account Holder, Admin, or App Manager. The new app lands in **Apps** with status **Prepare for Submission**. ([developer.apple.com/help/app-store-connect/create-an-app-record/add-a-new-app](https://developer.apple.com/help/app-store-connect/create-an-app-record/add-a-new-app), field rules from [developer.apple.com/help/app-store-connect/reference/app-information/app-information](https://developer.apple.com/help/app-store-connect/reference/app-information/app-information))

Fields you set at creation — several are **permanent**, so slow down here:

| Field | Rules |
|---|---|
| **Name** | 2–30 characters, per localization. Editable until submitted; after that, only when the version is in an editable status. Another developer using your name can trigger a trademark claim. |
| **Bundle ID** | Must match the bundle ID in your project (`ios.bundleIdentifier` in Expo app config). **Cannot be changed after you upload a build.** Uploads are matched to the app by bundle ID + version + build string. |
| **SKU** | Internal only, never shown to customers. Letters, numbers, hyphens, periods, underscores (can't start with a hyphen, period, or underscore). **Cannot be changed after the app is added.** Pick something boring like `urgewalk-ios`. |
| **Primary language** | Default language for metadata; changeable later. |
| **Primary + secondary category** | Set in App Information; should reflect what the app actually does (e.g. Health & Fitness vs. Lifestyle is a real choice for a recovery app — it also affects the medical-declaration rules in §6). |
| **Apple ID** | Auto-generated numeric ID for the app. You'll need it for EAS Submit (`ascAppId`, §3). |

### Version vs. build number — the rule that bites everyone once
- **Version number** (`CFBundleShortVersionString`; `version` in Expo app config) is the user-facing "1.0" shown on the product page. You set it deliberately for each store release; EAS does not manage it for you.
- **Build number** (`CFBundleVersion`; `ios.buildNumber` in Expo app config) is developer-facing and **must increase with every upload** — reusing a build number is a common cause of upload rejection.
- **[Recommendation]** Expo's recommended setup: in `eas.json` set `"cli": { "appVersionSource": "remote" }` and `"autoIncrement": true` on the production build profile. EAS then stores and bumps the build number server-side so you never collide. ([docs.expo.dev/build-reference/app-versions](https://docs.expo.dev/build-reference/app-versions/))

---

## 3. Build & upload

### 3.1 Produce the build — [Required]
Expo path:

```bash
eas build --platform ios --profile production
eas submit --platform ios
```

- `eas submit` walks you through picking the build (or use `eas build --platform ios --auto-submit` to chain them). First run prompts for your Apple ID; the `.ipa` uploads to App Store Connect and, after processing, appears in TestFlight. Expo says processing **usually takes 10–15 minutes**; Apple emails you when processing completes. Your `eas.json` submit profile takes `submit.production.ios.ascAppId` — the app's Apple ID from App Store Connect → App Information → General Information. For non-interactive/CI submits you need an App Store Connect API key configured (via `eas credentials`). ([docs.expo.dev/submit/ios](https://docs.expo.dev/submit/ios/))
- **Other upload paths** Apple supports: Xcode, Transporter, altool, Swift Playgrounds, Xcode Cloud. ([developer.apple.com/help/app-store-connect/manage-builds/upload-builds](https://developer.apple.com/help/app-store-connect/manage-builds/upload-builds)) For an Expo project without a Mac, EAS Submit is the practical one.
- Toolchain note from the same page: iOS apps must currently be **built with Xcode 26 or later**. With EAS Build this is Expo's image choice, not yours — but it's why an EAS build config pinned to an old image can fail upload.

### 3.2 Export compliance — the "Missing Compliance" prompt — [Required to resolve per build]
If your app uses encryption — including ordinary HTTPS — App Store Connect wants an export-compliance determination before the build can be submitted. Apple frames it broadly: apps using standard encryption algorithms or crypto inside Apple's OS are in scope for the *determination*, even when no documentation ends up required. Determining it correctly is your legal responsibility under US export rules (and France has its own controls on some app categories). ([developer.apple.com/help/app-store-connect/manage-app-information/overview-of-export-compliance](https://developer.apple.com/help/app-store-connect/manage-app-information/overview-of-export-compliance))

The standard case for an app like this — encryption only via iOS frameworks / HTTPS (TLS), nothing proprietary — is **exempt**, and you can answer once, permanently, in the binary instead of per-build:

- Set the Info.plist key **`ITSAppUsesNonExemptEncryption` to `false`**. Per Apple's documentation for the key, `false` means the app doesn't use encryption, or only uses encryption that is exempt from export-compliance documentation. With the key set, App Store Connect stops asking on every submission. In an Expo project this goes in app config under `ios.infoPlist`:
  ```json
  "ios": { "infoPlist": { "ITSAppUsesNonExemptEncryption": false } }
  ```
- **[Conditional — your app implements its own/proprietary or non-standard encryption]** Then documentation **is** required: submit it in App Store Connect, wait for approval (the build sits in status **Waiting for Export Compliance** while a CCATS is reviewed), and attach it to the build. Don't claim the exemption inaccurately — Apple explicitly puts that liability on you.
- If a build shows **Missing Compliance**, you resolve it either by answering the encryption questions for that build in App Store Connect, or by shipping the key above in the next build.

*Verification note: Apple's standalone documentation page for the key ("Complying with encryption export regulations") would not load during this research pass, so the key's definition above is quoted via Apple's App Store Connect Help overview (fetched directly) and secondary sources quoting the same Apple doc — see "Gaps" at the end. The mechanism (set the key to skip per-build questions) is confirmed by the official Help page.*

---

## 4. TestFlight — test before you submit

Sources: [developer.apple.com/testflight](https://developer.apple.com/testflight/) and Guideline 2.2 ([guidelines](https://developer.apple.com/app-store/review/guidelines/)).

- **Internal testers — [optional, recommended].** Up to **100** people from your own App Store Connect team (Account Holder, Admin, App Manager, Developer, or Marketing roles). You can auto-distribute new builds to them. No Apple review of the build.
- **External testers — [Conditional: anyone outside your team tests].** Up to **10,000** testers, invited by email or public link. Before external testers get anything you must add **test information** (what to test), a **feedback email**, a **beta app description**, and **beta app review information** in the TestFlight tab — and the **first build must pass Beta App Review**: builds are automatically sent for review when you add them to an external group. Guideline 2.2 adds: a beta build that changes significantly should go back through TestFlight App Review before going to testers.
- Testers use the TestFlight app, can test on up to 30 devices each, and send feedback (annotated screenshots, crash reports) that lands in App Store Connect. You can have up to 100 builds available and test multiple builds at once.
- **What TestFlight does NOT do:** it is **not** App Store review and doesn't substitute for it. When you're done testing, you select the build in App Store Connect and submit it for full App Review separately. TestFlight apps must still comply with the Review Guidelines, and paying/compensating TestFlight testers in any form violates 2.2. Demos and betas don't belong on the App Store itself — TestFlight is their home.

---

## 5. Product page & metadata

Field rules: [platform version information reference](https://developer.apple.com/help/app-store-connect/reference/app-information/platform-version-information) and [app information reference](https://developer.apple.com/help/app-store-connect/reference/app-information/app-information). Accuracy rules: Guideline 2.3 ([guidelines](https://developer.apple.com/app-store/review/guidelines/)).

- **Name** — 2–30 characters (see §2). **Subtitle** — up to 30 characters, shown under the name. Don't stuff either with trademarks, other apps' names, prices, or claims not specific to the field (2.3.7). **[Required fields]**
- **Promotional text** — up to 170 characters, sits above the description, and is the one field you can edit **without** a new submission. [Optional]
- **Description** — **[Required]**, up to 4,000 characters, plain text (no HTML), localizable. 2.3.2: if you mention In-App Purchases anywhere (description, screenshots, previews), they must be flagged clearly; misleading content here is a removal-level offense (2.3.1).
- **Keywords** — **[Required]**, up to 100 bytes total, comma-separated, each over 2 characters. Your app name and company name are already searchable — don't waste bytes repeating them; other companies' names are prohibited.
- **Screenshots** — **[Required]**. JPG/PNG, no transparency, minimum 1 and up to 10 per device size per localization. Required sizes:
  - **iPhone: at least one screenshot for an iPhone with Dynamic Island** — Apple's Help lists the *medium* display as the required one (accepted portrait: **1179×2556** or **1206×2622**). Uploading the largest size (large display: up to **1320×2868**) lets App Store Connect scale down to the rest. ⚠️ Expo's store-assets guide phrases the minimum as the **6.9″** size (1320×2868 / 1290×2796). **Safest move: upload the large-display set — it satisfies both descriptions.** (Flagged in Gaps.)
  - **iPad: only if your app supports iPad** — at least one 13″ screenshot (**2064×2752** or **2048×2732**). If your Expo app is iPhone-only (`supportsTablet: false`), no iPad set is needed.
  - Content rule (2.3.3): screenshots must show **the app in use** — not just a title screen, login, or splash. ([screenshot specifications](https://developer.apple.com/help/app-store-connect/reference/app-information/screenshot-specifications), [docs.expo.dev/guides/store-assets](https://docs.expo.dev/guides/store-assets/))
- **App previews (video)** — [Optional] up to 3 per localization per device size, and they may **only** be screen captures of the app itself (voiceover and overlays are fine) — 2.3.4.
- **Support URL** — **[Required]**. Must lead to a page with a real way to contact you (1.5: the app and its support URL must include an easy way to reach you; the reference notes support pages may need legal address/email/phone depending on local law). A dead or placeholder URL is a 2.1 rejection.
- **Marketing URL** — [Optional].
- **Copyright** — **[Required]**: year + rights holder (App Store Connect adds the © itself).
- **Routing App Coverage File** — **[Conditional]** Only for routing apps (apps giving directions) — a `.geojson` with a single MultiPolygon. Not relevant to a walking-companion app that doesn't provide routes.
- **Icon** — comes from the app bundle automatically on iOS (no separate upload, unlike Google Play). ([docs.expo.dev/guides/store-assets](https://docs.expo.dev/guides/store-assets/))

---

## 6. Privacy, age rating, and legal declarations

### 6.1 App Privacy ("nutrition label") — [Required]
In App Store Connect → your app → **App Privacy**. Rules: [developer.apple.com/app-store/app-privacy-details](https://developer.apple.com/app-store/app-privacy-details/)

- You must disclose **all** data that **you or your third-party partners** collect — "third-party partners" explicitly includes analytics tools, ad networks, and **third-party SDKs**. If an SDK in your Expo bundle phones home, it's your disclosure. You're responsible for keeping the answers accurate and current; they can be updated any time without an app update.
- "Collect" means transmitting data off the device in a way you or a partner can access beyond what's needed to service the request in real time. **Data that never leaves the device isn't "collected"** — which is the whole advantage of a local-first app: an honest "Data Not Collected" label is available if it's actually true (check your SDKs — crash reporters and analytics count).
- There's a narrow **optional-disclosure** carve-out, but it only applies if *all* of Apple's listed criteria are met (not used for tracking/ads/marketing, infrequent, not core functionality, user-provided in the UI with clear notice each time, etc.). Don't stretch for it.
- For each data type you declare: whether it's **linked to the user's identity**, and whether it's used for **tracking** (linking your data with third-party data for targeted advertising/measurement, or sharing with a data broker). Data types include Health & Fitness, Location (precise/coarse), Identifiers, Usage Data, Diagnostics, User Content, Sensitive Info, and more.

### 6.2 Privacy policy — [Required]
Guideline **5.1.1(i)**: **all apps** must link to a privacy policy **both** in the App Store Connect metadata field **and** inside the app in an easily accessible place. The policy must: identify what data (if any) is collected, how, and all uses; confirm any third parties you share data with get the same or equal protection; and explain retention/deletion plus how a user revokes consent and/or requests deletion of their data. The Privacy Policy URL field itself is required for iOS apps in App Information. ([guidelines](https://developer.apple.com/app-store/review/guidelines/), [app information reference](https://developer.apple.com/help/app-store-connect/reference/app-information/app-information))

### 6.3 Age rating — [Required; unrated apps can't be published]
Set via the questionnaire in App Information (App Store Connect auto-calculates a global rating plus region-specific ones). Answer honestly — 2.3.6 makes dishonest answers a violation. ([set an app age rating](https://developer.apple.com/help/app-store-connect/manage-app-information/set-an-app-age-rating), [age rating values](https://developer.apple.com/help/app-store-connect/reference/app-information/age-ratings-values-and-definitions))

- Current rating values on devices running **iOS 26+: 4+, 9+, 13+, 16+, 18+** (12+/17+ are the older set; the displayed rating depends on the user's OS).
- Questionnaire sections: **In-App Controls** (parental controls, age assurance), **Capabilities** (unrestricted web access, user-generated content, social media, messaging/chat, advertising), **Mature Themes** (profanity, horror, alcohol/tobacco/drug use or references), **Medical or Wellness** (medical/treatment information; health or wellness topics — e.g. exercise recommendations), **Sexuality or Nudity**, **Violence**, **Chance-Based Activities** — each answered None / Infrequent / Frequent.
- Rating math that matters for an 18+ recovery app: *frequent* alcohol/tobacco/drug references → **18+** territory; *infrequent* references land at 13+. Medical/treatment information: infrequent is allowed from 13+, frequent from 16+.
- **Override to Higher Age Rating:** if your app's own terms require a minimum age above what the questionnaire calculates, you **must** override upward. An app whose EULA says 18+ but whose questionnaire math lands at 13+ needs the override — the rating is app-level and applies across platforms.

### 6.4 Other legal declarations in App Store Connect
- **Content Rights — [Required to answer].** Apps that contain, show, or access third-party content must have all necessary rights for every territory they're distributed in. ([app information reference](https://developer.apple.com/help/app-store-connect/reference/app-information/app-information))
- **EU Digital Services Act (DSA) trader status — [Conditional: distributing in the EU].** App Store Connect asks whether you're a "trader" under the EU DSA. Traders must provide identification/contact details that are **displayed publicly on the EU product page**. (Same reference.)
- **Regulated Medical Device declaration — [Conditional].** Apps available in the EU/EEA, UK, or US whose primary/secondary category is **Health & Fitness or Medical**, *or* whose age rating shows "Frequent" Medical/Treatment information, **must complete this declaration**. If Urge Walk ships in Health & Fitness, expect this step. (Same reference.)
- **Territory-specific filings** (China ICP filing, Korea/Vietnam game licensing, Korea reference numbers for certain categories with frequent drug/alcohol references) exist but are **[Conditional]** on territory + category choices — opting out of those territories avoids them entirely. (Same reference.)

---

## 7. Pricing & availability

Source: [set a price](https://developer.apple.com/help/app-store-connect/manage-app-pricing/set-a-price) — **pricing must be set before you submit for review.**

- **Free is a price.** If the latest Paid Apps Agreement isn't signed/accepted, free is the *only* option — Apple won't let you set a paid price.
- Apple no longer uses fixed "tiers": you pick a price for a **base country/region** and Apple generates equivalent prices across the other **174 storefronts / 43 currencies** (175 storefronts total). Up to **800 price points** by default (more on request). Prices you set manually per-storefront are *not* auto-adjusted when Apple updates FX/tax conversions — the base-price approach is.
- **Availability:** choose which of the 175 territories to distribute in; an app missing a required regional age rating shows as unavailable there ("Missing App Rating" availability status). ([app and submission statuses](https://developer.apple.com/help/app-store-connect/reference/app-information/app-and-submission-statuses))
- **Release timing** is set per version (§10): manual, automatic on approval, or automatic on/after a date.
- **Pre-orders — [Optional].** A never-published app can be offered for pre-order after approval (statuses "Pre-Order Pending Developer Release" / "Pre-Order (Scheduled Release)" exist for this), and Guideline 2.3.11 requires the pre-order build to be complete and deliverable as submitted; a material change to the app during pre-order means restarting pre-order sales. **[Recommendation]** Skip pre-orders for a first launch — manual release gives you the same control with less surface area. (Apple's dedicated pre-order setup page would not load during research — exact setup steps unverified; see Gaps.)
- **In-App Purchases / subscriptions** each get their own product records and their own review (see 3.1.x in §9). [Conditional]

---

## 8. Submitting for App Review

### 8.1 Before You Submit — Apple's own list
From the guidelines' introduction ([guidelines](https://developer.apple.com/app-store/review/guidelines/)): test for crashes and bugs; make sure **all** app information and metadata is complete and accurate; update your contact information; provide App Review **full access** — an active demo account or a fully-featured demo mode, plus any hardware/resources review needs; **enable your backend services** so they're live during review; and put **detailed explanations of non-obvious features and IAPs** in the Notes for Review. Apps are checked against the guidelines in effect on the submission date.

### 8.2 App Review Information (per version) — [Required]
Set on the version page; invisible to customers; editable any time. ([platform version information](https://developer.apple.com/help/app-store-connect/reference/app-information/platform-version-information))

- **Contact info:** name, email, phone — phone must be in international format (+country code; bare numbers are rejected).
- **Sign-in required + demo account:** if the app needs a login to be used, you must provide a demo username/password, and **the demo account must not expire**. Extra accounts or setup details go in Notes.
- **Notes:** up to 4,000 bytes, any language — app-specific settings, how to register/test an account, anything non-obvious.
- **This is where the 2.5.19 checklist plugs in.** Guideline 2.3.1(a) requires that *all* new features, functionality, and product changes be described with specificity in the Notes for Review (generic descriptions get rejected), and 2.5.19 itself bans hidden/dormant/undocumented features — "functionality should be clear to end users and App Review," and hiding features (e.g. via obfuscation) is grounds for removal and account termination. Run `APP-STORE-2.5.19-CHECKLIST.md` (this folder) on the exact build you're submitting, and paste its review-notes template output here.

### 8.3 What happens after you submit
Statuses, from [app and submission statuses](https://developer.apple.com/help/app-store-connect/reference/app-information/app-and-submission-statuses):

- **Ready for Review** → **Waiting for Review** (Apple has it, review hasn't started; you can still edit some app info or pull the build, but screenshots/previews freeze) → **In Review**.
- Outcomes: **Pending Developer Release** (approved, waiting on you — manual release), **Processing for Distribution** (live within ~24 hours), **Ready for Distribution**, **Rejected**, **Metadata Rejected** (fix the *metadata* and reply to the App Review message — no new binary needed), **Invalid Binary** (upload a compliant build), **Accepted** (one item in a multi-item submission passed while another didn't — nothing publishes until all items pass), **Developer Rejected** (you pulled it yourself), **Waiting for Export Compliance** (§3.2).
- You can submit multiple items (app + IAPs, etc.) in one submission; items with issues can be removed from the submission while accepted items continue.

### 8.4 Timelines — only what Apple actually publishes
Apple's App Review page ([developer.apple.com/app-store/review](https://developer.apple.com/app-store/review/)) states: App Review works around the clock and **typically reviews at least 50% of submissions in less than 24 hours and 90% in less than 48 hours**. You get an email on every status change. Apple's own caveats: incomplete submissions may be delayed or not pass at all, and the guidelines page adds that complex apps or new issues can take longer — and that **repeated rejections for the same violation, or trying to manipulate the process, make future reviews take longer**. No other official numbers exist; anything more precise you read elsewhere is anecdote.

### 8.5 If you get rejected — the loop
- **First move: talk to App Review in App Store Connect.** Apple explicitly says to communicate with the review team directly through App Store Connect messages first (the rejection includes the guideline cited and, per the App Review page, usually the steps to take next). Well over **40% of unresolved issues relate to Guideline 2.1** (crashes, placeholder content, incomplete metadata/info) — most rejections are fixable paperwork or a demo-account problem, not a verdict on the app.
- **Appeal — [Conditional: you believe Apple misunderstood the app or you were treated unfairly].** You can appeal to the **App Review Board**. Constraints: give specific reasons the app complies; answer any requests for more information *before* appealing; and **only one appeal per submission that didn't pass review**.
- **Expedited review — [Conditional, narrow].** Only for (a) a **critical bug fix** — include steps to reproduce the bug in the current *live* version, or (b) an app **tied to an event you're directly associated with** — include the event, its date, and your association, and have the release already planned in App Store Connect. It is not a "please hurry" button, and Apple notes abuse can get future expedite requests rejected.
- **Bug-fix fast lane:** for an app already on the store, bug-fix submissions won't be delayed by guideline violations found along the way (except legal/safety ones) — Apple offers this via a message in App Store Connect, and you address the issue in your next submission.
- You can also book a **30-minute App Review appointment** (video call) to discuss guidelines, best practices, and common rejection reasons.

---

## 9. The Review Guidelines, family by family — plain language

Full text: [developer.apple.com/app-store/review/guidelines](https://developer.apple.com/app-store/review/guidelines/) (page footer: **last updated October 16, 2026** — 2.5.19 was newly added in that revision, and 1.2's wording was clarified). Items marked **[Conditional]** only apply if the condition is true of your app.

### Family 1 — Safety
- **1.1 Objectionable content.** No defamatory/discriminatory content, realistic depictions of killing/torture, weapons encouragement, sexual/pornographic material (including "hookup" apps), inflammatory religious commentary, false information or trick functionality (fake location trackers etc.), or concepts that profit from current sensitive events. "Shocking for shock's sake" isn't entertainment to Apple.
- **1.2 User-generated content — [Conditional: users can create/generate/upload/share content in the app].** As clarified in the Oct 16, 2026 revision, such apps must include **all four**: (1) filtering for objectionable material, (2) a mechanism to report offensive content **with timely responses**, (3) the ability to block abusive users, and (4) published contact information. You must actually remove violating content and act on reports; failing to implement safeguards, or failing to remediate after Apple notifies you, can also violate the Developer Code of Conduct. *Even a simple "share your walk notes" or community feature triggers this whole section.*
- **1.4.1 Medical & health claims — the one written for apps like this.** Medical apps "may be reviewed with greater scrutiny." Apps that **claim to measure health metrics** must clearly disclose the data and methodology supporting the accuracy claims, and if the methodology can't be validated, the claim gets rejected — apps claiming to measure x-ray, blood pressure, temperature, blood glucose, or blood oxygen using only the device's sensors are flatly not permitted. Apps **should remind users to check with a doctor** in addition to using the app and before making medical decisions. Apps that need regulatory clearance should submit a link to that documentation. (1.4.2: drug dosage calculators must come from an approved institutional source or have regulatory approval. 1.4.3: apps that **encourage consumption of tobacco/vape products, illegal drugs, or excessive alcohol are not permitted** — a recovery-support app is the opposite of this, but it means the app's content must never read as encouraging use, and facilitating sale of controlled substances is banned outside licensed pharmacies/legal dispensaries.)
  - **Practical translation [Recommendation]:** keep claims to what the app actually does ("helps you ride out an urge with a walk"), never diagnosis/treatment language ("treats addiction"), include a plain "this isn't medical advice — talk to a doctor" disclaimer in the app, and be ready to explain any health-adjacent feature in the review notes.
- **1.5 Developer information.** Your app and Support URL must include an easy way to contact you.
- **1.6 Data security.** Apps must implement appropriate measures against unauthorized access to user data and features.
- **1.7** Apps for reporting alleged criminal activity must involve local law enforcement (rarely relevant).

### Family 2 — Performance
- **2.1 App completeness — the most common rejection family (see §8.5).** Submissions must be **final versions**: complete metadata, fully functional URLs, no placeholder content, no references to "test/beta" in the app's metadata or notes. Apps must be **tested on a real device** for crashes. Provide demo account info (or a built-in demo mode — which itself needs prior Apple approval if a demo account can't be provided for legal/security reasons). **Backend must be on during review.** Incomplete app bundles and binaries that crash or show technical problems get rejected. 2.1(b): IAPs must likewise be complete, up-to-date, visible to the reviewer, and functional — explain in review notes if any IAP can't be found.
- **2.2 Beta testing.** Betas/demos/trials go through TestFlight, never the App Store (§4).
- **2.3 Accurate metadata.** The §5 rules: specific Notes for Review (2.3.1(a)), IAP disclosure, screenshots showing the app in use, previews from real captures, honest age-rating answers, 30-character name limit, no keyword-stuffing with trademarks or other apps' names.
- **2.5.19 No hidden features.** No hidden, dormant, or undocumented features; functionality clear to users and to App Review; concealment (e.g. obfuscation) risks removal + account termination. → **Run the companion checklist: `APP-STORE-2.5.19-CHECKLIST.md`.**

### Family 3 — Business
- **3.1.1 In-App Purchase — [Conditional: the app sells/unlocks any digital goods or features].** Unlocking features or functionality — subscriptions, premium content, full-version upgrades, in-game currency — **must use Apple's IAP**. You may not use your own unlock mechanisms (license keys, QR codes, crypto wallets, etc.). Rules inside it: IAP credits/currency may not expire; provide a **restore mechanism** for restorable purchases; digital gift cards/certificates/vouchers redeemable for digital goods may only be sold via IAP. On the **US storefront**, the guideline text itself notes the prohibition on buttons/links steering users to other purchase mechanisms doesn't apply — don't build a submission strategy on region-specific payment carve-outs without reading 3.1.1(a) and 3.1.3 closely. *A completely free app never touches 3.1.1.*
- **3.1.2 Subscriptions — [Conditional: auto-renewable subscriptions].** Must deliver ongoing value (content, services, cloud storage, continued feature access, etc.); subscription period at least 7 days; must work across all the user's devices; can't condition delivery of what was paid for on unrelated tasks (posting to social media, check-ins); switching monetization models can't strip functionality existing users already paid for; and before purchase you must clearly describe what the user receives.
- **3.1.3** Carves out defined categories (reader apps, multiplatform services, enterprise, person-to-person services, goods/services consumed outside the app, free stand-alone apps) that may use other payment flows — each with its own conditions. [Conditional]

### Family 4 — Design
- **4.2 Minimum functionality.** The app must include features, content, and UI that lift it **beyond a repackaged website**; apps that aren't useful, unique, or "app-like" may not be accepted. 4.2.2: not primarily marketing material, ads, web clippings, content aggregators, or a collection of links. 4.2.3: must work standalone — if it downloads extra resources needed to function at first launch, it must disclose the size and prompt the user first. 4.2.6: apps built from a template/app-generation service are rejected unless submitted by the content provider themselves (an Expo app you wrote and submit under your own account is fine).
- **4.3 Spam.** (a) Don't create multiple bundle IDs of essentially the same app — pick the one bundle ID and iterate on it. (b) Apps indistinguishable from what's already abundant get rejected; saturated categories require a **meaningfully different or improved experience**. *A calm, local-first recovery walker with a real point of view clears this; a reskinned generic habit template wouldn't.*
- **4.5.4 Push notifications — [Conditional: the app sends push].** Push must not be **required** for the app to function; no sensitive/personal/confidential info in notifications; and no promotional or direct-marketing push unless users **explicitly opted in** through in-app consent UI **and** have an in-app way to opt out. For a recovery app, notification *content* matters doubly — an urge-related message on a lock screen is exactly the "sensitive info via push" fact pattern.
- **4.8 Login services — state this precisely.** **IF** your app uses a **third-party or social login** (Facebook Login, Google Sign-In, Log in with X, Sign In with LinkedIn, Login with Amazon, WeChat Login) to set up or authenticate the user's **primary** account, you must **also** offer an equivalent alternative login service that: (i) limits data collection to name + email, (ii) lets users keep their email address private at sign-up, and (iii) doesn't collect interactions with your app for advertising without consent. (Sign in with Apple is Apple's own implementation of exactly this.) **It does NOT apply if**: the app exclusively uses your *own* account system; the login is specific to an alternative marketplace; it's an education/enterprise/business app using an existing edu/enterprise account; it uses a government/industry-backed citizen ID system; or the app is merely a client for one specific third-party service that requires direct sign-in. **And per 5.1.1(v), if the app has no significant account-based features, people must be able to use it without a login at all** — the cleanest position for a local-first app is: no account, no 4.8 question.

### Family 5 — Legal
- **5.1.1 Privacy & data collection.** (i) Privacy policy required — see §6.2. (ii) Collecting user or usage data requires **permission**; purpose strings must clearly and completely describe the use; consent must be withdrawable as easily as it was given. (iii) **Data minimization** — only collect what's directly relevant to core functionality; don't collect for unrelated purposes. (iv) Respect the user's permission choices — never trick or force people into consenting, and never manipulate them out of Apple services like iCloud/HealthKit. (v) **Accounts:** if the app supports **account creation, it must also offer account deletion inside the app** — and may not require personal information to function unless it's directly relevant to core functionality or legally required; social-login credentials must be revocable from within the app. (ix) **[Conditional]** Highly regulated fields / apps requiring sensitive user information should be submitted by the legal entity providing the services, not an individual (see §1.1 note). (x) Requests for basic contact info (name/email/phone) must be optional and must not gate functionality.
- **5.1.2 Data use & sharing.** No using, transmitting, or sharing personal data without permission (e.g. silently harvesting contacts). You must clearly disclose **where** personal data will be shared with third parties — **including third-party AI** — and get explicit permission before sharing. Tracking across other companies' apps/sites requires App Tracking Transparency consent.
- **5.1.3 Health data.** Health/fitness/medical data may not be used or disclosed for advertising, marketing, or other use-based data mining (except improving health management or approved health research); **never write false or inaccurate data into HealthKit**; personal health information may not be stored in iCloud; health research on human subjects needs consent + independent ethics-board approval.
- **5.1.5 Location.** Use location services only when directly relevant to the app's features, with notification and permission first. [Conditional: the app uses location — a walking app likely does, so the purpose string and the App Privacy location answer both need to be honest.]
- **5.2 Intellectual property.** Only ship content you created or are licensed to use (5.2.1); 5.2.3: an app must not make it easy to save/convert/download media from third-party sources (Apple Music, YouTube, SoundCloud, Vimeo, etc.) without explicit authorization.
- **5.6 Developer Code of Conduct.** Treat App Review and customers respectfully; manipulation and dishonest conduct risk account termination. (5.3 gambling, 5.4 VPN, 5.5 MDM exist but don't apply to this app.)

---

## 10. Release & updates

- **Release options per version** ([platform version information](https://developer.apple.com/help/app-store-connect/reference/app-information/platform-version-information)): **Manual** (approval → status Pending Developer Release → you press release), **Automatic** (goes live as soon as it's approved), or **Automatic, but no earlier than a chosen date** (if approved early it waits in Pending Developer Release and auto-releases on the date). After release, allow **up to 24 hours** for the app to appear on all selected storefronts.
- **[Recommendation]** First launch: choose **Manual**. Approval landing at 3 a.m. before you've checked the build one last time is the classic self-own; manual release costs you nothing.
- **Phased release — [Conditional: version *updates* only, not the first release].** For an automatic update you can opt into a 7-day gradual rollout to users with automatic updates on: **Day 1: 1%, Day 2: 2%, Day 3: 5%, Day 4: 10%, Day 5: 20%, Day 6: 50%, Day 7: 100%**. Anyone can still grab the update manually from the store during the phase. You can pause (up to 30 days total, unlimited pauses) and resume where it left off, or hit "Release to All Users" to end it early. Removing the app from sale kills a phased release permanently. ([release a version update in phases](https://developer.apple.com/help/app-store-connect/update-your-app/release-a-version-update-in-phases))
- **Updates vs. new builds.** Every code change = a new build upload with a **higher build number** (§2); a store-visible change = a new **version** record, which needs its own metadata pass, a **What's New** text (required for every version after the first, up to 4,000 characters), and its own trip through review. Metadata-only fixes after a **Metadata Rejected** outcome don't need a new binary (§8.3).
- **The rejection loop in practice:** rejection email cites the guideline → read it in App Store Connect → fix binary or metadata (or answer questions — sometimes review just needs information) → reply/resubmit in the same submission thread → back to In Review. Escalate to an appeal only when you genuinely believe the app complies as-is (one appeal per submission), and to expedited only for critical live bugs or tied events (§8.5). Note Apple's warning: resubmitting the same violation repeatedly makes future reviews slower, so fix the cited thing, not just the symptom.

---

## 11. End-to-end checklist — first submission, in order

**Accounts & agreements**
- [ ] Enroll in the Apple Developer Program ($99/yr) — Individual, legal name, 2FA on. (§1.1)
- [ ] *(Only if selling anything)* Account Holder signs the **Paid Apps Agreement** (Business → Agreements). (§1.2)
- [ ] *(If paid)* Complete the **W-9** and add **banking information** (tax forms must precede banking processing). (§1.2)

**Project setup (Expo)**
- [ ] `ios.bundleIdentifier` set in app config; matches what you'll register in App Store Connect. (§2)
- [ ] `eas.json`: `appVersionSource: "remote"` + `autoIncrement: true` on the production profile. (§2)
- [ ] `ios.infoPlist`: `ITSAppUsesNonExemptEncryption: false` *(only if your encryption is standard HTTPS/OS-provided)*. (§3.2)
- [ ] Permission purpose strings for everything the app requests (location, notifications…) — specific, honest. (§9, 5.1.1(ii)/5.1.5)
- [ ] EAS managed credentials on (or your own certs/profiles ready). (§1.3)

**App record & metadata (App Store Connect)**
- [ ] Create the app: name (≤30 chars), primary language, bundle ID, SKU (permanent — choose carefully). Status: Prepare for Submission. (§2)
- [ ] Set primary/secondary category; grab the app's Apple ID → `ascAppId` in `eas.json`. (§2, §3.1)
- [ ] Write description (≤4,000 chars), keywords (≤100 bytes), subtitle (≤30 chars), promotional text (≤170 chars). (§5)
- [ ] Screenshots: app-in-use, iPhone Dynamic Island set (upload the large 1320×2868 set) + iPad 13″ set *only if* iPad is supported. (§5)
- [ ] Support URL live with a real contact path; marketing URL (optional); copyright filled in. (§5)
- [ ] Privacy policy published on the web; URL entered in App Information **and** linked inside the app. (§6.2)
- [ ] App Privacy answers completed — audit your SDKs first; "Data Not Collected" only if it's true. (§6.1)
- [ ] Age rating questionnaire answered honestly; **override upward** if your terms say 18+ but the math says less. (§6.3)
- [ ] Content Rights answered; EU DSA trader status answered *(if distributing in the EU)*; Regulated Medical Device declaration *(if Health & Fitness/Medical category or frequent medical info)*. (§6.4)
- [ ] Price set (Free counts) and territories chosen. (§7)

**Build, test, submit**
- [ ] `eas build --platform ios --profile production`, then `eas submit --platform ios`. (§3.1)
- [ ] Build processed; export compliance resolved (key in plist, or answer the questions — no "Missing Compliance"). (§3.2)
- [ ] TestFlight: smoke-test the exact production build internally on a real device. (§4)
- [ ] *(Optional)* External TestFlight group → beta description/review info in → Beta App Review passed. (§4)
- [ ] **Run `APP-STORE-2.5.19-CHECKLIST.md` against this exact build** — flag inventory, denylist grep of the production bundle, demo access verified. (§8.2)
- [ ] App Review Information filled in: contact (phone with +country code), demo account that doesn't expire *(if login exists)*, Notes for Review containing the 2.5.19 template output + explanations of every non-obvious feature and IAP. (§8.2)
- [ ] Guideline self-check against §9: 1.4.1 claims/disclaimer, 1.2 *(if any UGC)*, 4.2 real functionality, 4.3 differentiation, 4.5.4 *(if push)*, 4.8 *(if social login)*, 5.1.1(v) account deletion *(if accounts exist)*, 3.1.1 *(if anything digital is sold)*.
- [ ] Version release option set to **Manual**. Submit for review. (§10)
- [ ] Watch for status emails: Waiting for Review → In Review → outcome. If rejected: reply in App Store Connect first; appeal/expedite only per §8.5. (§8.3–8.5)
- [ ] On approval (Pending Developer Release): final check of the listing → release. Live on storefronts within ~24 hours. (§10)

---

## Gaps & things I could not verify from official docs in this pass

Being explicit rather than smoothing these over:

1. **Export-compliance key definition.** App Store Connect Help's overview page (official, fetched) confirms the mechanism — answer the determination questions, set the Info.plist key to skip them per-build. But Apple's dedicated documentation page for `ITSAppUsesNonExemptEncryption` ("Complying with encryption export regulations") failed to load twice, so the exact wording of what counts as "exempt" in §3.2 is per Apple's Help page + consistent secondary quoting of that doc. The Expo-side location (`ios.infoPlist`) is Expo's standard config mechanism, corroborated by community sources, but I did not open an Expo doc page that names this specific key.
2. **Screenshot minimum — Apple vs. Expo wording.** Apple's screenshot spec page says the required iPhone set is the Dynamic Island **medium** display (1179×2556 / 1206×2622) and Expo's store-assets guide says the minimum is the **6.9″** set (1320×2868 / 1290×2796) and "2–10" screenshots vs. Apple's "at least 1." Apple's page governs; uploading the largest set satisfies both framings, hence the recommendation in §5.
3. **TestFlight build expiry.** The widely repeated "builds expire after 90 days" figure does not appear on the TestFlight page as fetched, so it's **not** stated in §4. Treat it as unverified here.
4. **Pre-order setup steps.** Apple's availability/pre-order Help URLs returned "Page Not Found" during this pass. Pre-orders' existence, their statuses, and the 2.3.11 completeness rule are verified (statuses reference + guidelines); the click-by-click setup and any date-window rules are not, and §7 is written accordingly.
5. **Tax/banking section naming.** Current App Store Connect Help routes everything through **Business → Agreements** (tabs/sections for Agreements, Tax Forms, Bank Accounts). Older third-party guides saying "Agreements, Tax, and Banking" as a standalone section name are describing the same area; the task brief's name for it is the legacy one.

---

## Verified on 2026-10-20 from:

**Apple — developer.apple.com**
- App Store Review Guidelines — https://developer.apple.com/app-store/review/guidelines/ (page footer: Last Updated October 16, 2026)
- App Review overview (timelines, appeals, expedited, bug-fix submissions) — https://developer.apple.com/app-store/review/
- Enroll in the Apple Developer Program — https://developer.apple.com/programs/enroll/
- Sign and update agreements — https://developer.apple.com/help/app-store-connect/manage-agreements/sign-and-update-agreements
- Provide tax information — https://developer.apple.com/help/app-store-connect/manage-tax-information/provide-tax-information
- Enter banking information — https://developer.apple.com/help/app-store-connect/manage-banking-information/enter-banking-information
- Add a new app — https://developer.apple.com/help/app-store-connect/create-an-app-record/add-a-new-app
- App information reference — https://developer.apple.com/help/app-store-connect/reference/app-information/app-information
- Platform version information reference — https://developer.apple.com/help/app-store-connect/reference/app-information/platform-version-information
- Screenshot specifications — https://developer.apple.com/help/app-store-connect/reference/app-information/screenshot-specifications
- Set an app age rating — https://developer.apple.com/help/app-store-connect/manage-app-information/set-an-app-age-rating
- Age ratings values and definitions — https://developer.apple.com/help/app-store-connect/reference/app-information/age-ratings-values-and-definitions
- App and submission statuses — https://developer.apple.com/help/app-store-connect/reference/app-information/app-and-submission-statuses
- Upload builds — https://developer.apple.com/help/app-store-connect/manage-builds/upload-builds
- Overview of export compliance — https://developer.apple.com/help/app-store-connect/manage-app-information/overview-of-export-compliance
- Release a version update in phases — https://developer.apple.com/help/app-store-connect/update-your-app/release-a-version-update-in-phases
- Set a price — https://developer.apple.com/help/app-store-connect/manage-app-pricing/set-a-price
- TestFlight — https://developer.apple.com/testflight/
- App Privacy details — https://developer.apple.com/app-store/app-privacy-details/

**Expo — docs.expo.dev**
- Submit to the Apple App Store (EAS Submit) — https://docs.expo.dev/submit/ios/ (page updated October 12, 2026)
- EAS Build introduction — https://docs.expo.dev/build/introduction/ (page updated July 22, 2026)
- App version management — https://docs.expo.dev/build-reference/app-versions/ (page updated September 25, 2026)
- Create and configure store assets — https://docs.expo.dev/guides/store-assets/ (page updated July 29, 2026)

**Companion document**
- `APP-STORE-2.5.19-CHECKLIST.md` (this folder) — Guideline 2.5.19 hidden-features checklist; run it at the step marked in §11.
