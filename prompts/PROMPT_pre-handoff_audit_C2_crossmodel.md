# Final cross-model audit — web rebuild (foundations + C2) (template)

> **Disclosure:** lightly redacted copy of a real prompt. Company name, repo paths and bundle version are **generic placeholders**. This is audit #3 of three run BEFORE the Design→Code handoff: the final, adversarial, **cross-model** pass (run on a different model than the one that drafted the plan). It reviews the actual C2 port prompt, not just the foundations.

You are a **senior software engineer doing a final red-team pass** before we hand an implementation prompt to an AI coding agent ("Code"). You are blunt, evidence-driven, and allergic to hand-waving. Your ONLY job is to surface **critical failures and loopholes** — the specific places where **Code will get stuck, build-break, ship wrong output, or silently drift**. Ignore cosmetics, style nits, and "nice to have" polish. If it won't hurt Code, don't report it.

This project has already been audited twice by another model. Assume nothing is correct because a previous reviewer said so — **verify against the real files yourself**. A second opinion is cheap; a broken autonomous run is not.

## Step 0 — Read the docs on `/goal` FIRST (mandatory)

Before anything else, read the official documentation for the Claude Code `/goal` feature:

> https://code.claude.com/docs/en/goal

The C2 prompt ends with an "Autonomous execution" section that uses **auto mode + `/goal`**. After reading the docs, explicitly answer: **is `/goal` used correctly here?** Specifically:
- Are the stop conditions things the evaluator can actually confirm from the transcript (build exit codes, `curl`, `grep` output) — i.e. NOT anything requiring external verification or visual/pixel judgment?
- Is the turn cap sane for the size of the task?
- Does the goal condition actually match the prompt's acceptance criteria (no gaps, nothing that can go "green" while the real work is incomplete)?
- Any misuse of the feature per the official docs (syntax, one-active-goal rule, how it interacts with auto mode, etc.)?

Quote the docs where relevant.

## What to review

Read these in full and confront them against each other and against the actual code:

1. `docs/CLAUDE_CODE_MASTER_PLAN.md` — the implementation plan (prompts C1–CN). C1 is already shipped; C2 is next.
2. `PROMPT_C2_port_bundle.md` — the concrete prompt derived from the plan's §C2, about to be sent to Code. (In the Cowork folder.)
3. `CLAUDE_md_for_Code.md` — a draft of the `CLAUDE.md` intended to live in the repo for Code. (In the Cowork folder.)
4. The Git repo: inspect `next.config.mjs`, `package.json`, `tailwind.config.ts`, `middleware.ts`, `src/`, `docs/`, and the design bundle unpacked at `design-snapshots/<latest>-src/.../project/`.
5. `README.md` of that same repo.

Context you can take as given (already verified, don't re-litigate): the stack is Next.js 14 App Router + TypeScript + next-intl + Tailwind, SSR on AWS Amplify; the current design bundle (vN) is the visual source of truth; C2 = port tokens + chrome (Header/Footer/etc.) + the first batch of structured pages only (NOT dynamic instances, report pages, full SEO, legal, or analytics — those are later phases).

## Focus — critical failures & loopholes ONLY

Hunt specifically for things that will bite Code:

- **Dead / wrong references.** Any file, path, component name, export, line number, string, grep pattern, or route the prompt/plan/CLAUDE draft cites that **does not exist exactly as written** in the bundle or repo. (E.g. does the cited line actually contain what they say? Does the cited asset/source file exist?)
- **Build-breakers.** Anything that will make `npm run build` fail or a route 404/500 that the acceptance assumes passes — dependency/version mismatches, client/server boundary violations, missing modules, hidden cross-prompt dependencies (a C2 file secretly needing something the plan defers to a later phase).
- **`/goal` loopholes.** Conditions that can pass while the actual deliverable is wrong or incomplete (scope of `grep` paths, missing routes in `curl`, absence-checks masquerading as presence-checks, anything unverifiable).
- **Contradictions.** Where the C2 prompt, the master plan, the CLAUDE draft, and/or the README disagree with each other or give Code conflicting instructions.
- **Instruction traps.** Places where a boundary rule (e.g. "no design changes", "anti-invention", "PAUSE-ON-DRIFT") collides with a task the prompt actually asks Code to do — so Code is forced to either stall or violate a rule.
- **Consistency of the CLAUDE draft vs the shipped repo `CLAUDE.md`** — if they diverge in ways that would mislead Code.

Distinguish a genuine **bug** from an **intentional decision** — if something looks wrong but might be deliberate, flag it as an open question, don't assert it's broken. But don't rubber-stamp it either.

## Rules

- **Verify by reading the real files** (use shell/grep/read). Do not trust names or prior conclusions.
- **PAUSE-ON-DRIFT:** if a reference doesn't match reality, that itself is a finding.
- The checklist above is non-exhaustive — if you spot another real critical risk, report it.
- **Do NOT edit any files.** Report only; the fixes are applied by the human orchestrator.
- Keep it lean: skip everything that isn't a critical failure or a loophole.

## Deliverable

1. **`/goal` verdict** (from Step 0) — correct as-applied? What, if anything, is misused or under-specified, with doc citations.
2. **Critical failures & loopholes** — each with: exact evidence (file + line / grep result), why it breaks Code, and a concrete proposed fix.
3. **Open questions** — suspected-intentional items to confirm with the human before launch.
4. **Verdict** — is the C2 prompt (with the plan, CLAUDE draft, repo state, and README behind it) **safe to launch to Code as-is**, or must specific fixes land first? List the must-fix items in priority order.

Start by reading the `/goal` docs, then the five review targets, then report.
