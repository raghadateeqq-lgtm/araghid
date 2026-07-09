# ARAGID · أراغيد

> **A daily emotional-reset companion — for the moments that go unspoken.**
> نظام إعادة ضبط عاطفي يومي — للحظات لا تُقال.

An Arabic-first, privacy-first mental wellness **Progressive Web App**. No backend, no accounts, no analytics — every entry stays on the user's device.

---

## The problem

Arabic speakers seeking mental wellness tools face three compounding gaps:

1. **Language.** Most wellness apps are English-first. Arabic support, when it exists, is a right-to-left retrofit — translated strings bolted onto a layout that was never designed for them. The result reads as foreign at exactly the moment a user needs it to feel like home.
2. **Privacy.** Journaling your hardest moments into someone else's cloud is a real deterrent. The most vulnerable entries are the ones least likely to be written if a server is involved.
3. **The gap between feeling and action.** In an acute moment — a panic spike, a 2 a.m. spiral — a blank journal box is the wrong interface. What helps is a short, structured, timed protocol.

**ARAGID** answers all three: Arabic as the design language rather than a translation target, `localStorage` as the only database, and a tiered **Rescue Hub** (30s / 90s / 7min) that meets the user at the intensity they're actually in.

---

## Key features

| Feature | What it does |
|---|---|
| **Rescue Hub** | Three escalating protocols — 30s, 90s, 7min — chosen by how much capacity the user has right now |
| **Mood system** | 8 emotional states logged *before and after* each session, so impact is measured rather than assumed |
| **Insights engine** | Detects 6 behavioural patterns: night-owl usage, recurring themes, improvement, momentum, decline, first-week |
| **EMDR** | Bilateral visual stimulation for self-guided grounding |
| **ندى (Nada)** | A context-aware companion that reads message tone and responds to the user's actual state |
| **Crisis detection** | Recognises high-risk language and surfaces Saudi Arabia's national mental-health line (٩٢٠٠٣٣٣٦٠) |
| **Guided writing** | 13 CBT-derived prompts, plus a Focus Mode that hides all chrome while writing |
| **Garden** | 7 growth milestones (1/3/7/14/21/50/100) that make consistency visible |
| **Letters to the future** | Write to a future self; delivered on a chosen date |
| **Weekly report** | Statistics + narrative + a shareable card |
| **Share cards** | Canvas-generated 1080×1920 PNG export |
| **Offline-first** | Full service-worker precache — every route works with no network |

**Free tier** (no signup): full Rescue Hub, unlimited writing, protocols, the Garden and Archive, 30 Nada messages/day, 3 letters, 1 weekly report per 7 days.
**ARAGID Full** (٢٩ ر.س/month · ٢٤٠ /year · ٧٤٩ lifetime): unlimited Nada and letters, the Monthly Reset ritual, PNG export for all sessions, advanced seasonal insights.

---

## Tech stack

Deliberately dependency-light — the entire app is served as static files.

- **HTML5** + **CSS3** with custom properties (a 5-layer token → component → screen → feature → premium cascade)
- **Vanilla ES Modules** — native `import`/`export`, **no bundler, no framework, no build step**
- **Service Worker** — cache-first for assets, network-first for HTML; full offline support
- **`localStorage`** with a hand-rolled schema-migration system
- **Canvas API** for PNG share-card generation
- **Web Share API** for native share sheets
- **Chart.js** (via CDN) — the single runtime dependency

Roughly **10,000 lines** across 19 JS modules and 5 CSS layers, with 13 client-side routes.

---

## Run locally

No install, no build. Any static file server works — but it **must be served over HTTP**, not opened as a `file://` URL, because ES modules and the service worker both require an origin.

```bash
git clone https://github.com/raghadateeqq-lgtm/araghid.git
cd araghid

# Python
python -m http.server 5700

# …or Node
npx serve . -l 5700
```

Then open **http://localhost:5700**

To verify the offline layer: DevTools → Application → Service Workers. You should see cache `aragid-v2.0.1`. Tick *Offline* and reload — every route still resolves.

---

## Project structure

```
araghid/
├── index.html              # Entry point — app shell
├── manifest.json           # PWA manifest
├── sw.js                   # Service worker (precaches all 19 modules)
├── ROADMAP.md              # Where this is going next
├── css/
│   ├── 01-tokens.css       # Design tokens — colour, type, spacing
│   ├── 02-components.css   # Reusable components
│   ├── 03-screens.css      # Screen layouts
│   ├── 04-features.css     # Feature-specific styles
│   └── 05-premium.css      # Premium-tier styles
├── js/
│   ├── app.js              # Bootstrap + router (13 routes)
│   ├── core.js             # Storage, migrations, quotas, helpers
│   ├── svg-defs.js         # Inline SVG icon sprite
│   ├── data-wisdom.js      # 25+ original Arabic reflections
│   ├── data-protocols.js   # 8 rescue protocols
│   ├── data-patterns.js    # 13 CBT writing patterns
│   ├── mood-system.js      # 8-state mood tracking
│   ├── insights-engine.js  # 6-pattern detection
│   ├── share-card.js       # Canvas → PNG export
│   └── views-*.js          # 10 view modules (home, nada, write,
│                           #   garden, emdr, letters, insights,
│                           #   rescue, pricing, other)
└── assets/icons/           # PWA icons (192px, 512px)
```

---

## Privacy

This is the architectural centre of the project, not a footnote.

- **All data is local.** `localStorage` on the user's device, and nowhere else.
- **No servers.** There is no backend to compromise. The app is static files.
- **No analytics, no tracking, no ads.** No third-party scripts beyond Chart.js.
- **No account, no signup.** Nothing to identify the user with.
- **Export/import as JSON** — the user owns their data and can take it out.

---

## What I learned

- **Right-to-left is an architecture decision, not a stylesheet flag.** Setting `dir="rtl"` is the last 5% of the work. Icon direction, animation origin, chart axes, gradient angles, swipe affordances, and the reading order of a stat row all carry directionality. Designing Arabic-first and treating LTR as the port — rather than the reverse — produced a fundamentally different and better layout.
- **Constraints clarify.** Choosing no framework and no build step meant every abstraction had to earn itself. Native ES modules turned out to be sufficient for a 13-route app, and the absence of a bundler made the service worker's precache list trivially auditable — I could read the entire dependency graph in one file.
- **Offline-first surfaces bugs that online-first hides.** Precaching forces you to enumerate every asset explicitly. That discipline caught a module (`views-insights.js`) that was silently missing from the cache list — it worked perfectly online and would have failed only for offline users, the hardest cohort to get a bug report from.
- **Measuring is a design feature.** Logging mood *before and after* a session, rather than just once, converts a journal into an instrument. It also creates an obligation: if the numbers say a protocol isn't helping, the protocol has to change.
- **Writing for someone in crisis is a discipline of subtraction.** Focus Mode began as a feature. It ended as a lesson: in the worst moments, the most valuable thing an interface can do is remove itself.

---

## Roadmap

Native iOS/Swift, on-device intelligence, deeper accessibility, and a fuller Arabic-first design system. See **[ROADMAP.md](ROADMAP.md)** for detail.

---

## A note on how this was built

This project was developed with **AI assistance**. I used AI tooling to accelerate implementation, explore approaches, and scaffold repetitive work.

Every decision that shaped the product is mine: the problem definition, the Arabic-first premise, the privacy architecture (no backend, no accounts), the three-tier rescue model, the content — all 25+ Arabic reflections, 8 protocols, and 13 writing patterns are original — and the choice to ship with no framework.

I reviewed, tested, restructured, and organised the entire codebase. I treat AI as a capable collaborator whose output requires judgement, verification, and ownership — not as a substitute for understanding my own system.

---

## License & credits

All content — reflections, protocols, and linguistic voice — is original to ARAGID.

**Crisis support (Saudi Arabia): ٩٢٠٠٣٣٣٦٠** — surfaced throughout the app wherever risk language is detected.

© 2026 ARAGID · Designed with care in Riyadh.
