# CopilotKit — Production Readiness Scorecard

A self-contained, interactive lead asset: 14 questions across the five production
bottlenecks → a score, the reader's weakest rung, and a "fix first" recommendation.
Self-segments by runtime status (prototype / staging / in-prod OSS) and surfaces a
"hidden tax" callout for teams already running self-hosted workarounds.

**One file. No build, no dependencies, no backend.** Everything runs client-side; nothing
the user enters leaves their browser.

Styled with CopilotKit's official brand system (from the `copilotkit-branding` +
`copilotkit-ui-theme` skills in `CopilotKit/internal-skills`): light `#f7f7f9` surface
with ambient lilac/mint edge glows, white cards, `#010507` ink, black pill CTAs,
Plus Jakarta Sans + Spline Sans Mono details, verified accent palette
(lilac `#BEC2FF`, mint `#85ECCE`/`#189370`, orange `#FFAC4D`, red `#FA5F67`),
and the authoritative logo asset (sourced from CopilotKit's own brand skills —
never redrawn).

## Files
- `index.html` — the scorecard (canonical, deployable at any path)
- `production-readiness-scorecard.html` — identical copy under a descriptive name
- `logo-full.svg` — official CopilotKit logo (referenced by the page)
- `vercel.json` — static hosting config (clean URLs + basic security headers)
- `_previews/` — reference screenshots (form + result; `brand-*.png` = current brand)

## Configure before shipping (30 seconds)
Open `index.html`, find the `CONFIG` block near the top of `<script>`:
```js
const REPLY_TO = "hello@copilotkit.ai";   // where "Email my results" sends
const BOOK_URL = "#book";                  // 20-min call link
```
- `REPLY_TO` — the inbox that should receive scorecard results (the "Email my results"
  button builds a mailto with the full diagnostic + segment prefilled, so a reply is your
  lead capture — no backend required).
- `BOOK_URL` — your booking link (Cal.com / Calendly / etc.).

## Host it on the CopilotKit site
It's a single static file — host it however the site serves static assets:
- **Next.js (app or pages):** drop `index.html` into `/public/scorecard/index.html`
  → live at `yoursite.com/scorecard`. (It's plain HTML, so keep it in `public/`, not as a
  React route.)
- **Any static host / CDN:** upload `index.html` to a `/scorecard` path. Done.
- **Embed:** it's also iframe-safe if you'd rather drop it into an existing page.

No env vars, no server routes, no dependencies to install.

## Optional: capture completions server-side
Today, lead capture is the mailto reply (zero infra). If you want silent analytics on
completion/score, add one line in `showResults()` to POST the summary to your endpoint or
fire an analytics event (e.g. `posthog.capture('scorecard_completed', {...})`). Hook point
is already isolated in `buildSummary()`.

## Deployed (interim)
Live on Vercel for the nurture launch:

**https://scorecard-nu-vert.vercel.app**  (stable production alias)

Project: `strategnik/scorecard`. Redeploy with `vercel deploy --prod --yes` from this folder.
Use the alias above — the per-deploy `*-strategnik.vercel.app` URL sits behind Vercel
deployment protection. Swap the alias for the CopilotKit-domain URL in Email 1 once the
scorecard is hosted on their site.
