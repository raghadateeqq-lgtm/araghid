# ROADMAP · خارطة الطريق

Where ARAGID goes next, and why.

ARAGID today is a static, offline-first PWA with no backend. That was the right starting constraint — it forced privacy to be architectural rather than promised. The roadmap below extends that premise rather than abandoning it: **every item must hold the line that a user's most vulnerable entries never leave their device.**

---

## 1. Apple ecosystem direction

The PWA proves the product. The Apple platforms are where it becomes a companion rather than a website.

- **Home Screen & Lock Screen widgets** — a Garden milestone or a one-tap Rescue entry point, reachable without opening the app. In an acute moment, every tap between the user and a 30-second protocol is a tap that loses them.
- **App Intents & Siri** — "Hey Siri, أراغيد إنقاذ" launching straight into the 30s protocol, hands-free.
- **HealthKit (read + write, opt-in)** — write completed sessions as Mindful Minutes; optionally read sleep and heart-rate variability to enrich pattern detection. Strictly opt-in, strictly on-device.
- **iCloud Private Database (CloudKit)** — sync across a user's own devices with end-to-end encryption, where Apple holds no readable copy. This is the *only* sync model compatible with the privacy premise.
- **Apple Watch** — a wrist-based breathing and grounding protocol with haptic pacing. Bilateral stimulation via alternating left/right taps is a natural fit for the EMDR module.
- **Live Activities** — a live session timer on the Lock Screen during longer protocols.
- **Focus filters** — surface the night-time ritual automatically when Sleep Focus engages.

---

## 2. A native Swift / iOS version

The web app's ceiling is real: no true background scheduling, limited haptics, no widget surface, and a service worker that Safari may evict under storage pressure. A native client removes all four.

**Proposed shape**

| Layer | Choice | Why |
|---|---|---|
| UI | **SwiftUI** | Declarative, and its RTL support is genuinely first-class rather than retrofitted |
| Persistence | **SwiftData**, on-device only | Replaces `localStorage`; the existing migration system maps cleanly onto schema versioning |
| Intelligence | **Core ML** + **Natural Language** | Runs the insights engine and crisis detection entirely on-device |
| Sync | **CloudKit private DB** | Opt-in, end-to-end encrypted, user-owned |
| Haptics | **Core Haptics** | Bilateral EMDR pacing and breath cadence through touch |

**Migration path.** The current codebase separates content (`data-*.js`), logic (`core.js`, `insights-engine.js`, `mood-system.js`), and presentation (`views-*.js`). Content ports to a Swift package as structured data with near-zero rework; the logic layer has clear, testable boundaries and translates directly. Only the view layer is a genuine rewrite — which is the point of going native.

The PWA remains the Android and desktop story. This is an addition, not a replacement.

---

## 3. Privacy-first local storage

Today: `localStorage`, with schema migrations and JSON export/import. It works, but it has three real limits worth naming — it's synchronous (blocking the main thread on large reads), capped at roughly 5–10 MB, and stored in plaintext.

Planned:

- **Migrate to IndexedDB** — asynchronous, orders of magnitude more headroom, and it stops blocking the main thread as journals grow.
- **Encryption at rest** — derive a key from a user passphrase via the Web Crypto API (`PBKDF2` → `AES-GCM`). A device compromise should not mean a readable journal.
- **Encrypted export bundles** — today's JSON export is plaintext. It should be a passphrase-protected archive.
- **Explicit, granular deletion** — per-entry, per-category, and a verified full wipe. Deletion must be real and visible, not a hidden flag.
- **A written data-retention policy in-app**, in plain Arabic. Users should not have to read source code to know where their words live.
- **Zero-knowledge sync** — if sync ever ships beyond CloudKit, it encrypts client-side first. The server stores ciphertext it cannot read.

---

## 4. Better mood insights

The current engine detects 6 patterns from a before/after mood pair. It is deliberately simple, and it is the area with the most headroom.

- **Correlation, honestly framed.** Surface which protocols precede genuine mood lift *for this specific user* — while never implying causation from a handful of sessions. Statistical humility is an ethical requirement here, not a nicety.
- **Confidence thresholds.** Say nothing until the data supports it. A pattern claimed on three data points is noise wearing the costume of insight, and a wrong insight about someone's emotional life is worse than silence.
- **Temporal patterns** — time-of-day, day-of-week, and seasonal rhythms, once enough history exists to justify the claim.
- **On-device NLP for themes.** Cluster recurring journal topics locally, via Core ML or a WASM model. No text ever leaves the device — this is non-negotiable and rules out every hosted LLM API.
- **Trajectory, not just state.** Direction of travel over weeks matters more than any single day's reading, and it protects against a bad day reading as a bad month.
- **Falsifiable protocol evaluation.** If the data shows a protocol consistently fails to help, retire or redesign it. The measurement has to be allowed to say no.

---

## 5. Accessibility

Honest assessment: this is where the project is weakest today, and it is the highest-priority work on this list. A wellness app that excludes disabled users has misunderstood what wellness means.

- **Full VoiceOver / screen-reader support** — correct semantics, ARIA landmarks, and a logical focus order. Arabic screen-reader behaviour needs testing specifically; it cannot be inferred from English results.
- **Complete keyboard navigation** — every route, protocol, and modal reachable without a pointer, with visible focus rings and no keyboard traps.
- **Respect `prefers-reduced-motion`** — the app leans on animation for calm, which becomes actively harmful for users with vestibular disorders. The EMDR module needs a carefully designed reduced-motion path, since motion *is* the mechanism there.
- **WCAG 2.2 AA contrast**, audited across both themes rather than assumed from the token palette.
- **Dynamic Type / user font scaling** — the layout must survive a 200% font size without clipping Arabic diacritics.
- **A non-visual EMDR mode** — bilateral *audio* and *haptic* stimulation, making the module usable by blind and low-vision users.
- **Cognitive accessibility** — plain-language mode, reduced-density option, and no time pressure anywhere in the interface.

---

## 6. Arabic-first UX

The premise of ARAGID is that Arabic is the design language, not a translation target. There is further to go.

- **A proper Arabic type scale.** Latin type scales transplant badly. Arabic needs its own line-height ratios and letter-spacing rules, because ascenders, descenders, and diacritics distribute differently down the line.
- **Diacritics (tashkīl) as an option.** The reflections currently carry partial vocalisation. It should be a user setting — helpful for some readers, visual noise for others.
- **Dialect awareness.** Modern Standard Arabic reads formal, sometimes clinical. Gulf and Levantine registers land more warmly in a moment of distress. Nada's voice should be tunable.
- **Hijri calendar support** alongside Gregorian, for the Garden, the Archive, and the Monthly Reset ritual.
- **Arabic-Indic numerals as a preference** (٠١٢٣ vs 0123) — the app currently mixes both, which is a real inconsistency.
- **RTL-native charts.** Chart.js axes still read left-to-right. Time should flow right-to-left, in the direction the user reads.
- **Culturally-grounded content review** — the protocols and reflections deserve review by an Arabic-speaking mental health professional before any clinical framing is claimed.

---

## Honest limitations

Stated plainly, because a roadmap that only lists ambitions is marketing.

- **This is not a clinical tool.** It is not a substitute for therapy, and no claim of clinical efficacy is made or implied. The crisis line is surfaced precisely because the app knows its own boundary.
- **The protocols are informed by CBT and EMDR literature but are not clinically validated** in this specific implementation.
- **Crisis detection is keyword-based** and will produce both false positives and — more seriously — false negatives. It is a safety net with real holes, and it is designed to fail toward showing the hotline rather than hiding it.
- **No data means no aggregate learning.** The privacy architecture forecloses learning across users. That is a deliberate trade, and the right one.
- **Accessibility is incomplete.** See section 5. Shipping is not the same as being usable by everyone.

---

*This roadmap is a statement of direction, not a delivery commitment. Sequence and scope will follow what users actually need — measured, where possible, rather than assumed.*
