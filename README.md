# ARAGID

Arabic-first emotional wellness platform designed to support daily recovery through guided sessions, journaling, emotional regulation tools and an offline-first Progressive Web App experience.

<div dir="rtl">

**أراغيد** — نظام إعادة ضبط عاطفي يومي، للحظات لا تُقال.

</div>

---

## Highlights

- **Arabic-first by design.** Right-to-left is the native layout, not a retrofit. Typography, iconography, motion origin, and reading order were built for Arabic and ported outward — not the reverse.
- **Privacy as architecture, not policy.** There is no backend. No accounts, no analytics, no telemetry. Every entry lives in the user's browser and nowhere else, which means there is no server to breach.
- **Offline-first.** A service worker precaches the entire application. Every screen works with no network connection.
- **Zero build step.** Native ES modules, no bundler, no framework, no transpiler. Clone it and serve it.
- **Measured, not assumed.** Mood is recorded before *and* after each session, so the effect of a protocol is observed rather than claimed.
- **Meets acute moments with structure.** A blank text box is the wrong interface during a panic spike. A tiered, timed protocol is the right one.

---

## Features

| Feature | Description |
|---|---|
| **Rescue Hub** | Three escalating protocols — 30 seconds, 90 seconds, 7 minutes — selected by the user's current capacity |
| **Mood system** | Eight emotional states, logged before and after every session to measure real impact |
| **Insights engine** | Detects six behavioural patterns: night-time usage, recurring themes, improvement, momentum, decline, and first-week onboarding |
| **EMDR exercise** | Bilateral visual stimulation for self-guided grounding |
| **Nada (ندى)** | A context-aware companion that reads message tone and responds to the user's present state |
| **Crisis detection** | Recognises high-risk language and surfaces Saudi Arabia's national mental-health line |
| **Guided journaling** | Thirteen CBT-derived writing patterns, plus a Focus Mode that hides all interface chrome while writing |
| **Garden** | Seven growth milestones that make consistency visible over time |
| **Letters to the future** | Messages written to a future self, delivered on a chosen date |
| **Weekly report** | Statistics, narrative summary, and a shareable card |
| **Share cards** | Canvas-generated 1080×1920 PNG export |
| **Protocol library** | Eight structured rescue protocols and a curated toolkit |

---

## Tech Stack

Deliberately dependency-light. The entire application ships as static files.

| Layer | Technology |
|---|---|
| Markup | HTML5, semantic landmarks, `lang="ar"` / `dir="rtl"` |
| Styling | CSS3 with custom properties, organised as a five-layer cascade |
| Logic | Vanilla JavaScript, native ES modules — no bundler, no framework |
| Offline | Service Worker (cache-first assets, network-first HTML) |
| Persistence | `localStorage` with a schema-migration system |
| Graphics | Canvas API for share-card generation |
| Charts | Chart.js via CDN — the sole runtime dependency |
| Sharing | Web Share API |

Approximately 10,000 lines across 19 JavaScript modules and 5 CSS layers, serving 13 client-side routes.

---

## Architecture

The application is a client-side single-page app with no server component.

```
index.html  →  app.js  →  router  →  view module  →  DOM
                  │
                  ├── core.js            storage, migrations, quotas, helpers
                  ├── mood-system.js     mood capture and trend derivation
                  ├── insights-engine.js pattern detection over local history
                  └── data-*.js          static content (protocols, patterns, wisdom)
```

**Four principles shape the structure:**

1. **Content, logic, and presentation are separated.** Static content lives in `data-*.js`, behaviour in `core.js` and the engines, rendering in `views-*.js`. This is what makes a future native port tractable — only the view layer is a genuine rewrite.
2. **`core.js` is the only module that touches storage.** Every read and write passes through a single `DB` interface, which is where schema migrations are applied.
3. **Views are pure render functions.** Each `views-*.js` module exports a `render*()` that builds and returns DOM. The router owns mounting; views own nothing global.
4. **The service worker's precache list is the dependency graph, written down.** With no bundler, the full asset manifest is a readable list in `sw.js` rather than build-tool output.

---

## Project Structure

```
araghid/
├── index.html              # Entry point — application shell
├── manifest.json           # PWA manifest
├── sw.js                   # Service worker — precaches all 19 modules
├── README.md
├── ROADMAP.md
├── css/
│   ├── 01-tokens.css       # Design tokens — colour, type, spacing, motion
│   ├── 02-components.css   # Reusable components
│   ├── 03-screens.css      # Screen layouts
│   ├── 04-features.css     # Feature-specific styles
│   └── 05-premium.css      # Premium-tier styles
├── js/
│   ├── app.js              # Bootstrap and router (13 routes)
│   ├── core.js             # Storage, migrations, quotas, shared helpers
│   ├── svg-defs.js         # Inline SVG icon sprite
│   ├── data-wisdom.js      # Original Arabic reflections
│   ├── data-protocols.js   # Eight rescue protocols
│   ├── data-patterns.js    # Thirteen CBT writing patterns
│   ├── mood-system.js      # Eight-state mood tracking
│   ├── insights-engine.js  # Six-pattern detection
│   ├── share-card.js       # Canvas to PNG export
│   └── views-*.js          # Ten view modules
└── assets/icons/           # PWA icons (192px, 512px)
```

---

## Getting Started

No installation and no build step. Any static file server works — but the app **must be served over HTTP**, not opened as a `file://` URL, because ES modules and the service worker both require an origin.

```bash
git clone https://github.com/raghadateeqq-lgtm/araghid.git
cd araghid

# Python
python -m http.server 5700

# ...or Node
npx serve . -l 5700
```

Then open **http://localhost:5700**

**Live demo:** [raghadateeqq-lgtm.github.io/araghid](https://raghadateeqq-lgtm.github.io/araghid/)

---

## Offline Support

The application is fully functional without a network connection.

- **Precache on install.** The service worker caches the app shell, all 19 JavaScript modules, all 5 stylesheets, the manifest, and both icons.
- **Cache-first for assets.** Static resources resolve from cache immediately, then fall back to the network.
- **Network-first for HTML.** The document is fetched fresh when online and served from cache when offline, so updates propagate without stranding offline users.
- **Versioned cache with cleanup.** Each release names a new cache; the `activate` handler deletes every stale cache before claiming clients.
- **Graceful degradation.** Cache failures are caught per-asset rather than failing the whole install, so a single unreachable resource cannot break registration.
- **Local persistence.** Because all data is in `localStorage`, there is nothing to sync and no offline write queue to reconcile.

To verify: open DevTools → Application → Service Workers, enable *Offline*, and reload. Every route resolves.

---

## Accessibility

**Implemented today:**

- Semantic landmarks — `<header>`, `<nav>`, `<main>`, `<section>` — with native `<button>` elements for all interactive controls
- `lang="ar"` and `dir="rtl"` declared at the document root and mirrored in the manifest
- `prefers-reduced-motion: reduce` honoured, disabling non-essential animation
- ARIA labelling on icon-only controls and dynamic regions
- Colour and type scales defined as tokens, enabling systematic contrast work

**Known gaps, stated honestly:**

Accessibility is the area of this project with the most remaining work, and it is the highest priority on the roadmap. Screen-reader behaviour has not been tested with Arabic voices; full keyboard navigation is incomplete; WCAG 2.2 AA contrast has not been formally audited; and the EMDR exercise has no non-visual equivalent, which makes it unusable without sight. A wellness tool that excludes disabled users has misunderstood its own purpose. See [ROADMAP.md](ROADMAP.md) for the plan.

---

## Privacy

- **All data is local.** `localStorage` on the user's device, and nowhere else.
- **No backend.** The application is static files; there is no server to compromise.
- **No analytics, no tracking, no advertising.** No third-party scripts beyond Chart.js.
- **No account, no signup.** Nothing exists to identify a user with.
- **Export and import as JSON.** Users own their data and can take it with them.

---

## Roadmap

Native iOS and the wider Apple ecosystem, on-device intelligence, encrypted local storage, deeper mood insights, comprehensive accessibility, and a fuller Arabic-first design system.

See **[ROADMAP.md](ROADMAP.md)** for the detailed plan and an honest account of the project's current limitations.

---

## Development Note

This project was developed with AI assistance. AI tooling was used to accelerate implementation, explore approaches, and scaffold repetitive work.

Every decision that shaped the product is my own: the problem definition, the Arabic-first premise, the privacy architecture, the tiered rescue model, the original content, and the choice to ship without a framework. I reviewed, tested, restructured, and organised the entire codebase.

I treat AI as a capable collaborator whose output requires judgement, verification, and ownership — not as a substitute for understanding my own system.

---

## License

All content — reflections, protocols, and linguistic voice — is original to ARAGID.

**ARAGID is not a clinical tool** and is not a substitute for professional care. Crisis support for Saudi Arabia is surfaced in-app wherever risk language is detected.

© 2026 ARAGID · Designed with care in Riyadh.
