# CLAUDE.md — the brain of Claude Code (template)

> **Disclosure:** lightly redacted copy of a real working document (the `CLAUDE.md` that lives in the Code repo). Company/product names, domains, IDs, tokens and third-party trademarks are **generic placeholders**. The methodology and the dev stack are shown as-is.

## What this project is

A full rebuild of **www.mywebsite.com**, migrating off Wix to a modern AWS-hosted stack. [Company description here]. The site is bilingual (EN + one other locale).

Three business divisions (they structure the site):

1. **Division 1** [description].
2. **Division 2** [description].
3. **Division 3** [description].

## Tech stack

- **Next.js 14** (App Router) + **TypeScript** (strict).
- **next-intl** i18n. `defaultLocale: "en"`, `localePrefix: "as-needed"`. English has **no prefix** (`/ai`); the other locale is prefixed (`/es/ai`). `middleware.ts`: `localeDetection` only on `/`; the URL is the source of truth everywhere else.
- **Tailwind CSS** + CSS variables. **Fidelity rule:** the Design bundle uses inline styles + component classes over CSS variables, NOT Tailwind utilities — port that approach as-is; Tailwind only for layout utilities with no drift risk. Do **not** re-style components into Tailwind utilities.
- **lucide-react** for icons.
- Content: Markdown/MDX under `docs/content/` (pasted, not authored by Code).
- **Hosting:** AWS Amplify Hosting, **SSR mode**, **Node 24**. **Deploy pipeline:** GitLab → AWS CodeCommit → Amplify.
- **DNS:** registrar → nameservers = CloudFlare.
- **Analytics:** GTM container `GTM-XXXXXXX` (gated on `NEXT_PUBLIC_GTM_ID`) + GA4 property `000000000` (connected inside GTM, not a direct tag). Key event `book_demo_click`.
- **No custom forms, no SES/Resend, no `api/` routes.** Contact = embedded appointment-scheduling iframe.

## Sources of truth (paste from these; never invent)

| Need | File |
| --- | --- |
| Full implementation plan (prompts C1–CN, specs, acceptance) | `docs/CLAUDE_CODE_MASTER_PLAN.md` |
| Design bundle (visual truth: tokens, components, templates, pages) | `design-snapshots/<latest>-src/.../project/` (unpacked; zip committed in `design-snapshots/`) |
| Canonical routes, hreflang pairs, 301 map | `docs/SLUG_INVENTORY.md` |
| Per-page meta/SEO, JSON-LD, sitemap/robots, 404 | `docs/SEO_INVENTORY.md` |
| Copy of the report/content pages | `docs/content/*.md` (CONFIG blocks) + `docs/CONTENT_INVENTORY.md` |
| Legal copy | `docs/LEGAL_DRAFTS_terms_cookies.md` + privacy page (legal email only where legally required, for data-rights requests; Terms & Cookies route to `/contact`, no email) |
| Brand foundations | `docs/BRAND_GUIDE.md` |

## Engineering principles (adapted from Karpathy's CLAUDE.md)

General coding behavior. Where a principle meets a project rule below, **the project rule wins** (noted inline). Bias toward caution over speed; for trivial tasks use judgment.

- **Think before coding.** State assumptions explicitly; if uncertain, ask. If multiple interpretations exist, present them — don't pick silently. If a simpler approach exists, say so and push back when warranted. Never hide confusion.
- **Simplicity first.** Minimum code that solves the problem; nothing speculative. No abstractions for single-use code, no unrequested "flexibility", no error handling for impossible cases. If 200 lines could be 50, rewrite. _(Applies to implementation logic — NOT to ported visuals, which are copied faithfully; see the fidelity rule.)_
- **Surgical changes.** Touch only what the task requires. Don't "improve" adjacent code, comments or formatting; don't refactor what isn't broken; match existing style even if you'd do it differently. Every changed line must trace to the request. Remove only orphans YOUR change created; flag pre-existing dead code, don't delete it.
- **Goal-driven execution.** Turn tasks into verifiable success criteria and loop until met ("fix the bug" → "write a test that reproduces it, then make it pass"). This IS our Autonomous execution: the prompt's acceptance criteria are the `/goal`; loop under auto mode until the transcript proves them.
- **Verify before done.** Never mark a task complete without proving it works — run the build, curl the routes, check counts/logs. Ask: "would a staff engineer approve this?" ⚠️ **Visual fidelity is not machine-verifiable** — build-green ≠ pixel-correct; that review is manual (the owner).
- **Elegance — but fidelity wins.** For non-trivial _implementation_ (routing, i18n, boundaries, data wiring): pause and ask "is there a more elegant way?" and prefer the clean solution. **This NEVER means restyling the ported design** — "improving" the bundle's markup/CSS into cleaner Tailwind is re-authoring = drift, and is forbidden. Elegance applies to logic, not to the visual port.
- **Autonomous fixing — within the boundary.** Given a build error, failing lint/CI, or a broken route: just fix it; point at the log/error and resolve it, no hand-holding (that's what auto mode + `/goal` are for). BUT if the fix would require a **design/visual/structural change or new copy**, STOP and refer it to Design/Cowork — don't improvise it.
- **Self-improvement loop.** Read `tasks/lessons.md` at session start. After ANY user/Cowork correction, append the pattern + a rule that prevents repeating it. Code chats don't share memory across phases, so this repo-local file is how lessons carry over.
- **Keep context clean.** Offload broad research/exploration to subagents (one focused task each) so the main context stays lean.

## Hard rules for Code

1. **Fidelity over refactor.** Port the Design bundle's visual output faithfully. Deviating by re-authoring styles = drift and is forbidden. Fidelity is **visual**: DO translate the bundle's prototype architecture (`window.*` globals, `go()` router, runtime icon scan) into real Next modules, `<Link>`, `next/font`, and `lucide-react` components — while preserving the pixels.
2. **Anti-invention.** If a text, price, URL, slug or meta value is not in a cited source → **STOP and ask**. Never improvise copy, pricing, or routes.
3. **Code does NOT touch design or author content.** All structure/visuals come from the bundle; all copy comes from a cited source. If asked for a design/visual/structural change: **warn, stop, and refer it to Claude Design.** Only exception: explicitly authorized 1-line string/href fixes.
4. **Plan-first.** Show a short bullet plan and wait for OK before executing.
5. **Conventional Commits**, atomic, ideally one per approved step.
6. **Each prompt is self-contained** and says whether it runs in the same chat or a new one, plus the model.
7. **Autonomous execution:** prompts close with an "Autonomous execution" section = **Auto mode** (approves tool calls within a turn) + **`/goal`** (runs between turns until a transcript-demonstrable condition + a turn cap is met). Visual fidelity is NOT auto-verifiable — it is reviewed manually.

## Routing & URLs

- English (no prefix): `/`, `/ai`, `/<section>`, `/<section>/<subpage>`, `/<dynamic>/[slug]`, `/contact`, `/privacy`, `/cookies`, `/terms`.
- Other locale (`/es/` prefix): the prefixed equivalents plus the legacy slugs preserved from the old site, and `/es/<dynamic>/[slug]`.
- **Slug strategy:** legacy slugs preserved for SEO; new/structured pages use English slugs in both locales.
- **301 map** lives in `next.config.mjs` (single source; do not recreate a parallel redirects module). CloudFlare only handles apex→www. All rules single-hop.
- **Sitemap:** canonicals only, with hreflang alternates. **Canonical host = `https://www.mywebsite.com`.**
- **LanguageSwitcher:** hreflang pair → navigate to the exact counterpart; single-locale pages → navigate to the destination locale home. Never 404.

## Booking (lead capture)

No email/phone on the site. Every CTA funnels to the locale contact page, which **embeds** the appointment-scheduling iframe (`height=1000` desktop / `1500` mobile, reserved CSS space → zero CLS). Routing is by locale:

- EN `/contact` — iframe `<APPOINTMENT_SCHEDULING_URL_EN>`
- Other locale `/es/contact` — iframe `<APPOINTMENT_SCHEDULING_URL_ES>`

CTA labels: "Book a demo" / localized equivalent (never "Book a call"). All CTAs route to `/contact` · `/es/contact`.

## Naming rules (EN / other locale)

- **Always keep in the original language (third-party trademarks):** `[vendor product names]` stay verbatim in both locales.
- **Translate generic category names** in EN (e.g. "Punto de Venta" → "Point of Sale", "Estados Financieros" → "Financial Statements").
- **Exception `demoLabel`:** when referencing a real demo whose own labels are in the other locale, keep that name even in EN.
- **Competitor product names**: allowed only in `keywords` meta, never in visible title/description/copy.

## Other fixed decisions

- **Schema.org:** Organization (`name: "My Company"`, `foundingDate: YYYY`, no address/phone/email) + Service ×N + BreadcrumbList + Product ×N (each carries `image` = the page's OG image, fallback `og-default.png`, and `brand`). `sameAs`: your public social profiles only. LocalBusiness omitted.
- **No cookie banner / consent mode** at launch (Cookie Policy page only).
- **Security headers** in `customHttp.yml` (root): HSTS (no `preload`), `X-Content-Type-Options: nosniff`, `Referrer-Policy`, `Permissions-Policy`. **CSP deferred to post-launch** (iframe / GTM / Google Fonts risk). Note: if `X-Robots-Tag: noindex` is applied site-wide, removing it at cutover also exposes the Amplify default domain to indexing → close that dup-content gap with a Next middleware host-check (301 to `www` when `host` ends in `amplifyapp.com`).
- **Footer:** rich, 4 columns (Products · Solutions · Company · Legal), cross-locale; mobile = sections fully expanded (no accordion).
- Core Web Vitals green; Lighthouse ≥90 (Perf/SEO/A11y/Best Practices). Mobile-first from 375px, touch targets ≥44px.

## Current status

> Per-phase status goes stale every session → it **lives in `PROJECT_STATE_LOG.md`** and in the phase table of `docs/CLAUDE_CODE_MASTER_PLAN.md`. Do not duplicate it here. [One-line fixed summary of the current phase goes here.]

## Workflow (brief)

Three coordinated chats: **Cowork** (strategy/orchestration — the brain), **Claude Design** (visual — owns the bundle), **Claude Code** (implementation — this repo). Design→Code handoff is one-directional: Code implements the bundle faithfully and only pastes content/strings/hrefs + implementation logic. If a design/structural need arises, stop and route it back through Cowork/Design.
