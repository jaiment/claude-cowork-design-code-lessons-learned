# Claude Design — Playbook (template)

> **Disclosure:** this is a lightly redacted copy of a real working document. Any IDs, tokens and URLs (design project ID, bookmarks, etc.) have been replaced with **synthetic placeholders**.

> **Source of truth** for operating Claude Design on this project.
> `PROJECT_STATE_LOG.md` and `COWORK_PROJECT_INSTRUCTIONS.md` point here instead of repeating the detail.

Claude Design is in **beta / research preview** and unstable. Treat it as a **visual prototyping** tool, NOT as storage. The truth lives in **git**. Its usage **counts against the shared plan limit** (there is no separate allowance).

Current project (Design System): `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

> Complements `COWORK_PROJECT_INSTRUCTIONS.md` (three-chat workflow, detailed inline-comment rule, Design best practices). **This playbook = how to operate the tool; COWORK = how it fits into the process.**

---

## 1. Mental model (hierarchy)

Organization → **Design System** → **Project** → **Template**.

- **Design System** = published, reusable foundation: `tokens/`, `components/`, `guidelines/`. Edited via *Remix*. If it's "Published", new projects inherit it.
- **Project** = an individual design that *consumes* the Design System.
- **Template** = a starting scaffold.

⚠️ **On this project the site does NOT live inside a Project**: it was built **inside the Design System**, as `ui_kits/marketing/` (it shows up as the card "Marketing site — interactive (EN/ES)"). That's the current reality; knowing it avoids confusion. **There is no official way to move a ui_kit into a Project.**

---

## 2. Golden rules

- **Design is untrusted storage.** The truth lives in git. Always back up.
- **Do NOT use "save current state."** It generates duplicate cards with the same name (it caused a messy incident once). ⚠️ Different from the per-chat versioning that the 2.0 docs do document ("Save what we have and try a completely different approach", see §6.5): that one is legitimate. For surgical edits we use neither — the discipline is export + commit (§3).
- **Canonical card via bookmark** (there is no rename/delete in the UI): `https://claude.ai/design/p/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx?file=ui_kits%2Fmarketing%2Findex.html`. Always enter through it; ignore old cards with the same name.
- **Magenta = AI surfaces only** (your AI product). Blue = primary, teal = accent.
- **Inline comments** = change to 1 element. **Chat** = multi-section or structural. **Edit on canvas** = quick aesthetic tweaks.
- **🔴 ALWAYS ask where new pages get generated — BEFORE drafting the prompt** (golden rule): any new page or instance has 3 options for "review/preview location" and I NEVER decide alone which one; **always ask the owner first**:
  - **(a) Extend `pilots.html`** with a new tab + selector — for parameterized instances of an existing template. Pattern already established with the `T1 · Instances` tab and its instances.
  - **(b) A separate `_preview-<page>.html` file** — for standalone pages that are NOT instances of a template.
  - **(c) An accordion or navigation inside a single card** — a mixed variant for small groups of related pages.

  Suggested initial criterion: if it's an **instance of an existing template** → (a). If it's a **standalone page** → (b). But **NEVER assume** — ask the owner in plain language: *"Does this go inside `pilots.html` as a new tab, or as a separate `_preview-<page>.html` file?"* before drafting any prompt section that touches the review harness. Lesson learned the hard way: I once drafted a prompt with several separate `_preview-<system>.html` launchers without asking; the owner caught the miss and asked to consolidate them into `pilots.html` with a single tab. This decision is **very important** because it affects future review discipline: scattering files breaks the flow of systematic QA.

---

## 3. Backup & verify discipline

For every approved milestone (without anyone asking):

1. **Export → Download .zip** (project archive) **AND** **Export → standalone HTML**.
2. Save as `design-snapshots/YYYY-MM-DD_design-vN_<slug>.zip` and `_standalone.html`.
3. Commit `chore(design): snapshot vN — <milestone>` + push. Extra copy in cloud storage (**3-2-1** rule).
4. **Verify the export** before trusting it: the canonical `index.html` (`ui_kits/marketing`) carries all pages, icons, and matches the approved milestone.

**Export formats** (Share button → Export): Download `.zip` (project archive) · standalone HTML · PDF · PPTX · Send to Canva · **Hand off to Claude Code**. The project-archive `.zip` is the **COMPLETE** Design System bundle (tokens, components, `_ds_bundle.js`, guidelines, ui_kits, templates, snapshots, assets, manifest) and includes a `SKILL.md` that makes it usable as an **Agent Skill** — useful for rebuilding or for handoff.

---

## 4. Gotchas

- **The `.zip` won't open locally.** The kit's `index.html` is a React+Babel loader that pulls external `.jsx`; the browser blocks them over `file://` → blank screen. **This is not corruption.** Verify in the live tool or with the standalone HTML.
- **The standalone goes stale after editing.** After changes, Design does NOT re-bundle the standalone on its own: you have to ask explicitly ("re-bundle the standalone export"). The project-archive `.zip` does reflect the current state on export.
- **The standalone HTML exports ONLY the page you're currently viewing, NOT the whole project.** It's a "single self-contained HTML file" per export. To back up the whole site you need MULTIPLE standalone downloads — one per page of the kit. To get the WHOLE project in a single file, use the `.zip` (project archive). **Correct backup order per milestone:** (1) ask Design to re-bundle the standalone, (2) navigate to canonical page A → export standalone, (3) navigate to page B → export standalone, (4) export `.zip` (carries everything), (5) verify standalones offline, (6) commit.
- **"Standalone variants" of pages (e.g. `ABPage.standalone.jsx`) do NOT auto-sync with the live page.** Some kits have separate `.standalone.jsx` files the bundle uses for offline; changes to the main `.jsx` do NOT propagate automatically. You have to explicitly ask Design to "port the changes into the standalone variant too" when re-bundling.
- **`[bundle] error` (red box) in the offline standalone** = external fetches failing (Google Fonts / Google Calendar iframe). **Cosmetic**; the design is fine.
- **Blank cards** (beta bug): old cards/snapshots may stop rendering the preview (the files aren't lost; the render fails). Can worsen during incidents on status.claude.com.
- **Rename/delete of cards: does NOT exist in the UI.** Only "Add usage notes / Feedback / Edit". Disambiguate with the canonical bookmark.
- **Unreliable multi-editing** + reports of **losing access to projects after canceling the subscription** → the git backup is critical.
- **Inline comments sometimes get lost** before Claude reads them → workaround: paste the feedback in the chat.
- **"chat upstream error"**: open a new chat tab inside the same project (don't retry in the same one).
- **The Design preview is NOT navigable like a real site.** The hrefs on the canvas are intentionally **click-guarded**. So: you cannot "navigate by URL" nor edit the path to reach a page or anchor — the browser URL is the **canvas**, not the page. The only way to reach a kit page is to **open it from the Design System Index** (left sidebar). Real routing + anchor-scroll verification = **Code phase**, not Design.
- **Duplicated cards/pages in the Index may show different versions.** When Design does Export or "save", it generates duplicates (same name, different timestamps). Some are the canonical one with recent changes, others are old sandboxes. **Always open the most recent one** (top of the list). Shared chrome (Header/Footer) applies to ALL pages that consume it, but a duplicated page "photographed" before the last change may look stale.
- **Project state persists across chats.** A "new" chat in Claude Design does NOT start tabula rasa — it inherits the project's current state (files created by earlier chats, even abandoned ones). Implication: if a surgical prompt returns a .zip with unanticipated files, before crying "scope creep" verify against the v(N-1) snapshot — it may be residue from prior exploration, not the current prompt.
- **Mobile screenshots from Design can be desktop-cropped, not a real viewport.** When Design hands you a 390px screenshot, it may be a desktop capture "cropped" to 390px width without triggering mobile media queries. If you see a mobile layout that doesn't respect breakpoints, verify in a real browser by shrinking the window before reporting a bug.
- **The "Undo" in Claude Design is per-turn, NOT navigable.** The small "Undo" button next to "Edited N files" reverts ONLY that turn. There is NO navigable history like Claude Code `/rewind`. **Operating rule**: do NOT use Undo blind when there's approved work after the turn you want to undo. The truth lives in git — use the already-exported `.zip` as source of truth and selectively commit what you want.
- **Helper components sometimes already support what you think is missing:** before asking to "extend component X with slot Y", READ the component's code. Real case: I was about to ask to extend a KPI list component with a `desc` slot, but on checking the code it ALREADY rendered `k.desc` — it just wasn't used because the data only passed `{ icon, title }`. VERIFY-BEFORE-PROMPTING applies to components you think need extending, too.
- **Pre-existing `url(#r)` warnings in standalones** (benign): Design's bundlers report warnings about `url(#r)` SVG fragment references in `styles.css` on every standalone export. NOT specific to the new work; ignore if present in the report.
- **Isolated PRE-prompt vs large prompt — operating criterion:** when a phase has multiple new components/extensions, evaluate whether each deserves its own isolated PRE-prompt (independent snapshot for before/after backup and selective rollback). Criterion: if a change touches **shared components / DS helpers / base patterns** (that other consumers inherit), isolate it in a PRE-prompt. If it only builds **pages that consume** existing components, it goes in the large phase prompt. Each PRE-prompt closes with its own vN snapshot before moving on.
- **Design's operational honesty:** when you ask it to confirm info ("re-list those 5 items"), if Design doesn't remember 5 real items it will explicitly say "there were 3, not 5, I don't want to invent two extra" instead of fabricating. Treat this as a positive signal, not a bug. Re-ask specifically for what's missing instead of accepting the first version without verifying.
- **Image perception limitation from Cowork**: I (Cowork) may NOT be able to read very small details in long screenshots the owner shares. **Operating rule**: if I need a specific detail from a screenshot and can't see it clearly, say honestly "I can't read that part, can you transcribe the block?" before guessing/assuming. Do NOT invent what you think it probably says.

---

## 5. Surgical prompt pattern (reusable template)

Works when: (a) you name the exact file/screen, (b) you list each specific change with its value, (c) you set hard constraints, (d) you ask it to report what changed.

```
Make exactly N targeted changes to <screen/file> ONLY. Do not modify any other
screen, component, or token, and do not regenerate the page.

1) <specific change with exact value>
2) <specific change…>

HARD CONSTRAINTS:
- Edit ONLY <file>.
- Do NOT change components, tokens, or any other page.
- Do NOT regenerate; make only these edits.
- Report the before/after of what you changed.
```

Channel rule: 1 element → inline comment · quick aesthetic → edit on canvas · multi-section → this prompt in chat.

---

## 6. Reading list (fetch at the start of sessions that touch Design)

**Official:**
- Get started — https://support.claude.com/en/articles/14604416-get-started-with-claude-design
- Design system setup — https://support.claude.com/en/articles/14604397-set-up-your-design-system-in-claude-design
- Admin guide (Team/Enterprise) — https://support.claude.com/en/articles/14604406-claude-design-admin-guide-for-team-and-enterprise-plans
- Tutorial prototypes/UX — https://claude.com/resources/tutorials/using-claude-design-for-prototypes-and-ux

**Bugs / instability:** check community reports and https://status.claude.com/ before each session.

---

## 6.5. Verified facts from the official research — how they inform prompts

Fetched from the official docs (Get started, Prototypes/UX tutorial, release notes) + the 2.0 update. Only what **factually** changes how I draft prompts to Design.

**Anatomy of an effective prompt (official docs).** A good prompt carries: **goal** (what you're building) + **layout** (how it's arranged) + **content** (what information) + **audience** (who uses it). Rules the docs confirm:
- **Be specific with feedback.** "It doesn't look good" isn't actionable; "tighten the spacing between fields to 8px" is.
- **Name components by their real name** ("use the Primary Button", "the Card pattern"). Names **cross into the handoff to Code** — hence VERIFY-BEFORE-PROMPTING: inventing a name that doesn't exist breaks the session.
- **Start simple and add complexity in layers** — Design responds well to incremental requests.
- **Think responsive from the start** (declare mobile/tablet/desktop in the prompt).
- **Ask for 2-3 variations** when the direction isn't clear.
- **Ask for edge cases BEFORE the handoff**: empty / error / loading states + different data volumes.
- **Ask Design to review its own output**: accessibility, contrast, information hierarchy. Design is a design collaborator, not just a generator.

**Chat vs inline comment vs edit-on-canvas.** The official docs confirm §2: **comment** = change to ONE specific component; **chat** = structural changes, new sections, or anything needing context (and ALL multi-section refactors); **edit on canvas** = quick aesthetic tweaks (drag/resize/align).

**Per-chat versioning (2.0) — important nuance.** The 2.0 docs document that you can tell Design *"Save what we have and try a completely different approach"* and it saves the project confirming where. **This is NOT the "save current state" action** that duplicated cards (§2). Per-chat versioning is legitimate for exploring directions; for surgical edits we use neither (export+commit, §3).

**2.0 capabilities relevant to us:**
- **Canvas editing** (drag/resize/align) → for quick aesthetic tweaks, not content changes.
- **Design system import** rebuilt (GitHub / design files / uploads / local codebase). Caveat: *"the import is only as good as its source"*. Relevant to the handoff to Code.
- **Shared usage limit**: Design no longer has a separate weekly allowance; it counts against the chat/Cowork/Code pool.
- **Large repos**: link from Claude Code / use `/design-sync`, don't load the whole tree in the browser.
- **Availability**: web + desktop only. Multi-person simultaneous editing is still basic/unreliable.

**Adherence self-check (2.0):** Design self-verifies against the Design System and self-corrects. Ask EXPLICITLY for the check report in each prompt ("run your design-system adherence check on everything you touched").

**PAUSE-ON-DRIFT rule (add to every surgical prompt §5):** "If any referenced line, selector or string does not match the current state of the file, PAUSE and report the mismatch instead of improvising an equivalent change." Protects against drift between our snapshots and the real project state (which persists across chats, §4).

**Handoff (confirmed by official docs + community):** the "Hand off to Claude Code" bundle includes design files + chat + README + a prompt with the bundle URL that Code consumes via API. The **component names used in the conversation cross into the bundle** — another reason to VERIFY-BEFORE-PROMPTING. Ask for edge cases (empty/error/loading/volumes) BEFORE the handoff, not after.

---

## 7. Postmortem — the "lost icons" scare

After a collaborative session it looked like "the icons were lost." **Real cause:** we were opening old/stale cards (templates without the icon set) and judging state by opening the `.zip` locally (loader → blank). **Nothing broke:** `ui_kits/marketing` (live) was complete, with icons and Contact. The confusion was caused by (a) building the site inside the Design System, (b) "save current state" generating same-name cards, (c) judging by the local `.zip`. **Fix:** canonical bookmark + verified git backup (3-2-1) + this discipline.

---

## 7.5. Same chat vs new chat (Design and Code)

**Fixed operating rule**: every time Cowork hands a prompt to Design (or Code), it MUST state explicitly whether it runs in the **same chat** or a **new one**. This applies to both Claude Design and Claude Code — both have cumulative context and the decision impacts tokens, performance and continuity.

**When SAME chat:**
- Short mini-prompt (targeted fix, re-bundle, copy tweak)
- Direct continuation of the previous prompt (the agent needs to remember what it did)
- Changes that depend on decisions made in the same session flow
- Current chat is under ~120k tokens

**When NEW chat:**
- Large/independent prompt (replicate templates, new project phase)
- Current chat shows the "Start a new chat to save X tokens" notice or exceeds ~150k tokens
- Scope is self-contained (the new prompt carries all needed context)
- Growing risk of "chat upstream error"

**Explicit trade-off**: a new chat saves tokens and improves performance but loses memory of the previous chat. Mitigation: the new prompt must be self-contained with enough context.

Cowork ALWAYS includes this indication when handing a prompt to the owner ("run this in the same chat" / "this one opens a new chat").

---

## 8. Handoff to Claude Code

- **Official route**: Export → **Hand off to Claude Code** generates a bundle (design files + session chat + README + copy-paste prompt). Destinations: "local coding agent" or "Claude Code Web". Also download the `.zip` and commit it to `design-snapshots/` as backup.
- **Slash commands** (Code↔Design, official docs): `/design-sync` (Code pulls the Design System so Design starts from the repo's real components) and `/design` (design/sync from the terminal). If they don't appear in Code, run `/update`; only new sessions bring them.
- Before the handoff: ask Design for the **edge cases** (empty / error / loading states) and **name components** by their name (they cross into the handoff).

---

_Keep this doc synced between the Cowork folder and the repo (`docs/`). Note: the rules also live in (1) Cowork's live project instructions and (2) the repo's `CLAUDE.md` — when Design rules change, sync all three copies._
