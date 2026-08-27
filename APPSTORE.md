# FLATPLAY → App Store: The Playbook

The goal: one iOS app called **FLATPLAY** containing the hub + SqueakStack +
INVADERBALL, free with ads + a "Remove Ads" purchase. The web versions stay
live and untouched.

The key idea for the Mac handoff: **nothing moves on a USB stick.** Everything
lives in GitHub (`richburg12/flatplay`). Ashley's laptop just clones it, does
the Apple-only steps, and pushes the results back.

---

## Phase 0 — Before touching any computer (browser, ~30 min + waiting)

1. Enroll in the **Apple Developer Program**: developer.apple.com/programs →
   Enroll → Individual → $99/year. Use whichever Apple ID you want to own the
   apps (yours, not Ashley's, so you keep control). Approval usually takes
   24–48 h. Nothing else can happen until this clears.

## Phase 1 — On your Windows PC, with your Claude (~an evening, mostly me)

1. Tell Claude "let's wrap FLATPLAY for iOS." It will: add Capacitor to the
   flatplay repo, bundle the hub + both games as local files (no internet
   needed in-app), generate the app icon and splash screen, and commit it all
   to GitHub. You review, that's it.
2. That push **is** the handoff. Write down two things to carry to the Mac:
   - your GitHub login (or just make the repo public — it already is)
   - the Apple ID + password (and its 2FA device) from Phase 0

## Phase 2 — On Ashley's MacBook (~2–3 hours first time, most of it downloads)

One-time setup (human steps):
1. Install **Xcode** from the Mac App Store. It's a huge download — start it
   first and let it run. Open it once, accept the license, let it install
   its components.
2. Sign into Xcode with the developer Apple ID: Xcode → Settings → Accounts →
   "+". This is the one step Claude genuinely can't do (2 minutes).
3. Make sure **Claude Code** is on her Mac (she may already have it; otherwise
   the desktop app or `npm install -g @anthropic-ai/claude-code`) and signed in.

Then open Claude Code in a terminal on the Mac and paste this prompt:

```
Clone https://github.com/richburg12/flatplay and get the iOS app building:
install anything missing (Homebrew, node, CocoaPods), run npm install and
npx cap sync ios, open the Xcode workspace, and walk me through setting the
Signing Team to my Apple Developer account with automatic signing. Then help
me: (1) run it on an iPhone plugged in over USB so we can test it, and
(2) Product > Archive and Distribute App > App Store Connect > Upload.
Narrate every Xcode click I need to make. When the upload succeeds, commit
and push everything you changed (including the ios/ folder) back to GitHub.
```

Claude drives the terminal; it narrates the handful of Xcode GUI clicks
(signing team, archive, upload) that need a human finger. Plugging in an
iPhone and running the app on it before uploading is optional but worth it.

## Phase 3 — App Store Connect (browser, back on any machine)

1. appstoreconnect.apple.com → My Apps → "+" → New App. Name: FLATPLAY.
   Bundle ID: the one from the project (Claude will have told you).
2. Fill the listing: description, keywords, screenshots (your Windows Claude
   can generate proper-sized screenshots from the games), support URL,
   privacy policy URL (a one-page "we collect nothing" — Claude can publish
   one on the hub site).
3. Age rating questionnaire → lands at 4+. **Do NOT opt into the Kids
   Category** — it bans normal ad networks. General audience, 4+ rating.
4. App Privacy: "Data Not Collected" (true today; changes when ads arrive —
   AdMob means declaring identifiers/ads data later).
5. Select the uploaded build, submit for review. Reviews take 1–3 days.
   First-time apps often bounce once on something trivial; fix, resubmit,
   don't take it personally.

## Handing it back / life afterwards

- After the Mac session everything — including the generated `ios/` project —
  is pushed to GitHub. Your PC pulls it and is fully up to date. Ashley's
  laptop keeps nothing except Xcode and the signing certificates (which
  Xcode manages; leave them).
- **Web updates stay Mac-free:** changing the games and pushing updates the
  live web versions instantly, like today.
- **App Store updates** (when you want store users to get the new build):
  back to the Mac for ~20 minutes — pull, `npx cap sync ios`, Archive,
  Upload, bump the version. Or, once this gets annoying, we wire GitHub
  Actions / Codemagic to build and upload from the cloud and the Mac retires
  from the process entirely.

## The ads + Remove Ads phase (when ready, adds a couple of evenings)

- **AdMob** via Capacitor plugin — banner or between-rounds interstitials,
  configured non-personalized (family audience). Needs an AdMob account.
- **Remove Ads**: a $1.99–2.99 non-consumable in-app purchase via StoreKit —
  use RevenueCat to skip receipt-validation pain. Apple requires a working
  "Restore Purchases" button. Apple's cut: 15% under the Small Business
  Program (auto-applies under $1M/yr).
- Both need one more Mac build + an updated App Privacy questionnaire.

## Cheat-sheet: what actually moves to the Mac and back

| Direction | What | How |
|---|---|---|
| PC → Mac | The entire app project | GitHub clone |
| PC → Mac | Apple ID + password + 2FA | Your head/phone |
| Mac → Apple | The built, signed app | Xcode upload |
| Mac → PC | ios/ project + any tweaks | git push / pull |
