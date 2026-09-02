# CopilotKit — Production Readiness Scorecard

A self-contained, interactive lead asset: 14 questions across the five production
bottlenecks → a score, the reader's weakest rung, and a "fix first" recommendation.
Self-segments by runtime status (prototype / staging / in-prod OSS) and surfaces a
callout listing the production responsibilities the team currently owns around the
runtime (self-maintained retry/failover, auth/tenant guard). Ownership questions
inform that callout only — they never affect the readiness score. The top tier
("Production-hardened") is suppressed whenever any single rung scores below 50%.

**One file. No build, no dependencies, no backend.** Scoring runs entirely client-side;
answers stay in the browser unless the user chooses to email or copy their results.

Styled with CopilotKit's official brand system (from the `copilotkit-branding` +
`copilotkit-ui-theme` skills in `CopilotKit/internal-skills`): light `#f7f7f9` surface
with ambient lilac/mint edge glows, white cards, `#010507` ink, black pill CTAs,
Plus Jakarta Sans + Spline Sans Mono details, verified accent palette
(lilac `#BEC2FF`, mint `#85ECCE`/`#189370`, orange `#FFAC4D`, red `#FA5F67`),
and the authoritative logo asset (sourced from CopilotKit's own brand skills —
never redrawn).

Floating dock (bottom-center): **Talk to an engineer** (copilotkit.ai/talk-to-an-engineer),
**Docs**, and **Discord** — same dock as the preference page.

## Files
- `index.html` — the scorecard (canonical, deployable at any path)
- `production-readiness-scorecard.html` — identical copy under a descriptive name
- `logo-full.svg` / `logo-mark.svg` — official CopilotKit logo (header + favicon)
- `vercel.json` — static hosting config (clean URLs + basic security headers)
- `_previews/` — reference screenshots (`brand-*.png` = current brand; `scorecard-*.png` = pre-brand originals)

## Configure before shipping (already set — verify only)
The `CONFIG` block near the top of `<script>` in `index.html`:
```js
const REPLY_TO = "hello@copilotkit.ai";   // where "Email my results" sends
const BOOK_URL = "https://www.copilotkit.ai/talk-to-an-engineer";  // 20-min call link
```
- `REPLY_TO` — the inbox that receives scorecard results (the "Email my results" button
  builds a mailto with the full diagnostic + segment prefilled, so a reply is your lead
  capture — no backend required). Change it if a different inbox should own these.
- `BOOK_URL` — points at Talk to an Engineer; swap if a dedicated booking link is preferred.
- The dock links (talk-to-an-engineer / docs.copilotkit.ai / discord.gg/copilotkit) are
  hardcoded in the `<nav class="dock">` at the bottom of the file.

## Host it on the CopilotKit site
Static files only — host however the site serves static assets:
- **Next.js (app or pages):** drop `index.html` + both logo SVGs into
  `/public/scorecard/` → live at `yoursite.com/scorecard`. (Plain HTML — keep it in
  `public/`, not as a React route.)
- **Any static host / CDN:** upload `index.html` + the two logo SVGs to a `/scorecard`
  path, keeping relative paths. Done.
- **Embed:** it's also iframe-safe if you'd rather drop it into an existing page.

No env vars, no server routes, no dependencies to install.

## HubSpot capture (added 2026-09-01)
On "See results," the page silently submits the full diagnostic to HubSpot form
`0eaf83fd-4bf2-418e-9d37-afcbfc9ee729` ("Scorecard · Production Readiness Results
(silent capture)", portal 45532593) — score, tier, segment, weakest rung, maintained
layers, and every one of the 14 answers verbatim (`cpk_scorecard_*` contact properties,
group "Scorecard · Production Readiness").

- **Identity:** the visitor's email comes from the page's own `?email=` param, or — when
  embedded same-origin (the site's `/scorecard` iframe wrapper) — from the parent page's
  `?email=` param. Email links must therefore append `?email={{ contact.email }}`.
- **No email → nothing recorded** (results still render; matches the preference page's
  forwarded-email behavior). Capture is best-effort: a failed POST never blocks the UX.
- The `hubspotutk` cookie is polled for 2s and attached so submissions tie to the
  contact's analytics history. `createNewContactForNewEmail` is off — unknown emails
  don't create junk contacts.
- Retakes that produce a changed result submit again (latest wins on the contact);
  identical re-renders are deduped client-side.

The "Email my results" mailto and copy-to-clipboard flows are unchanged on top of this.

This file is the canonical source; the live page is served from CopilotKit's website
repo at `/campaign-pages/scorecard/index.html` inside the `/scorecard` route's iframe —
hand them this file to update the deploy. (Current canonical = their deployed version
(a11y + PostHog upgrades) + the capture patch.)

## Deployed (interim)
Live on Vercel for the nurture launch:

**https://scorecard-nu-vert.vercel.app**  (stable production alias)

Project: `strategnik/scorecard`. Redeploy with `vercel deploy --prod --yes` from this folder.
Use the alias above — the per-deploy `*-strategnik.vercel.app` URL sits behind Vercel
deployment protection. Swap the alias for the CopilotKit-domain URL in Email 1 once the
scorecard is hosted on their site.
