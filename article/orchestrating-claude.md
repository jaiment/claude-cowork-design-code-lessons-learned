# 50% technical / 50% human: Orchestrating Claude Design, Cowork + Code

**Lessons from rebuilding a company website in under a month, almost solo.**

After recently helping a friend take his first steps with Claude Cowork, I realized AI creates a very different experience for each person. And it's not about the models' skills or their training. It's about how much each of us understands it, and how willing we are to ADAPT to this technological revolution. That's why I decided to share my story: a very personal one, 50% technical and 50% human. If you just want the public files I share on GitHub, scroll straight to the end. If you're more into "the joy is in the journey" like me, then maybe this is for you.

For years our site lived on Wix. It did the job, but every time I wanted to change something structural I hit a wall, and the coordination between content changes and SEO optimization was never a strength there. Meanwhile my X feed was full of noise about Claude Design but I wasn't really sure how to get started.

Back in June the company launched a new product and we needed not just a website refresh, but rebuilding it from the ground up. Caveat: I needed to port all of our old slug inventory and maintain some consistency between the new pages and the "transferred" old ones. So I started my first prompt on Claude Cowork (Opus 4.X models).

The "aha" moment came when I wanted to give Claude Design a try, but didn't want to write every prompt by hand for Design and Code. That's why I tried to commit to a coordination model: Cowork (the brain and architect in charge) handed specific tasks to Design and Code, and I pasted their output back into Cowork and worked it there: asking questions, digging in, challenging its reasoning and auditing the work with it before anything moved forward.

## The three-agent setup

I ran three separate Claude agents, each with a fixed role:

* **Cowork: the brain.** This was my business decision partner and the only agent I spoke to in Spanish. Cowork made no coding nor design. It made the Google Search Console and Google Analytics historical analysis, SEO optimization, drafted the prompts I fed to the other two agents, reviewed their output, and owned the source-of-truth documents ← BTW, I was the one suggesting how to split & organize documentation and stay on top of every important documentation aspect.
* **Claude Design: the design expert.** It owned the design system: project, templates and components. It worked with the prompts created with Cowork and once done, I exported the project ZIP so Cowork could audit the changes. ← BTW, I did a lot of manual/visual review using a localhost browser and caught a LOT of stuff.
* **Claude Code: the backend developer.** It executed the hand-off from the Design ZIP, did the wiring and porting copy-out texts to the templates. It transformed the design bundle faithfully into a real Next.js app, wired routing, i18n, metadata and redirects, and ran the builds. English only. I had little trouble working with Code, for me, this was the most precise agent.

The whole thing looked like this:

```
┌─────────────────────────────────────────────────────────────┐
│                    COWORK  (the brain)                      │
│          Role: Business decisions, create prompts           │
│                                                             │
│  - Project scope & goals, URLs, palette, copy               │
│  - Drafts the prompts for Design and Code                   │
│  - Reviews outputs (Design screenshots, source-code audits) │
│  - Analyze web traffic history and propose SEO improvements │
│  - Owns the source-of-truth docs (plan, brand, project log) │
│  - The only agent I speak with                              │
└─────────┬────────────────────────────────────┬──────────────┘
          │ visual prompts                     │ technical prompts
          ▼                                    ▼
┌────────────────────────┐         ┌────────────────────────┐
│  CLAUDE DESIGN         │         │  CLAUDE CODE           │
│  Role: UI/UX lead      │         │  Role: backend dev     │
│                        │         │                        │
│  - Tokens, prototypes, │         │  - Reads the repo +    │
│    UI kits, templates  │         │    the handoff bundle  │
│  - Produces the        │         │  - Implements, commits,│
│    handoff bundle      │         │    builds, deploys     │
│  - English             │         │  - Audits, fixes bugs  │
└─────────┬──────────────┘         └────────────▲───────────┘
          │                                     │
          │        handoff bundle (ZIP)         │
          └─────────────────────────────────────┘
```

Notice what flows where. Strategy only ever comes out of Cowork. The two specialists never talk to each other directly. The only channel between them is that handoff bundle at the bottom, and (as you'll see) that single arrow turned out to be the most dangerous part of the technical side.

Cowork was the one I had the most arguments and disagreements with. I had to pay a LOT of attention and catch errors in strategy & design, and I was the one with the most courage when we needed to recognize errors, step back and fix course.

## The founding move that set everything up

One of the first and most important moments happened when I handed Cowork our brand logo manual (a PDF) and the folder of logo assets. That's it.

Cowork documented them into a proper foundation, extracting the palette, the typographic intent, the asset inventory and the usage rules, and that documentation became the base of the design system that Claude Design built on top of.

That's when a bigger realization landed: the entire visual layer had to be built in Design, and Code should ONLY be used for backend wiring and implementation.

## How the coordination actually worked

A few rules turned three independent agents into something that behaved like a team.

**Every prompt was self-contained.** The agents don't share memory. A prompt to Code couldn't assume it remembered anything from last week. It had to carry its own context, its scope, and its acceptance criteria every single time.

**Surgical scope, always.** Each prompt listed exactly what changed and where, and ended with some version of "don't touch anything else." No "while you're at it." The fastest way to land in Alice in Wonderland is to let an agent use creativity beyond the task.

**Propose before prompting.** Before Cowork wrote a single prompt for Design or Code, it first told me, in plain Spanish, what it was about to ask for. I reviewed and questioned a lot, then approved the intent before it drafted the instruction.

**Same chat or new chat, and which model, every time.** Because each chat accumulates context, I had to force Cowork to always tell me whether a prompt ran in the same chat or a fresh one, and which model to use.

**One step at a time.** No multi-step "do A, then B, then C" plans handed to me to execute. One action, close it, move to the next. And at least for me, please 🙏 NO "AskUserQuestion" tool.

## Closing a session ← the ritual nobody talks about

Here's the part that surprised me most.

Every session close took an hour or more: not of building, but of analyzing and summarizing the state of the project and documenting, finding loopholes, linting and updating all relevant MD files:

* Master implementation plan
* [Cowork project instructions](https://github.com/jaiment/claude-cowork-design-code-lessons-learned/blob/main/docs/COWORK_PROJECT_INSTRUCTIONS.md)
* Project state log
* [CLAUDE.md for Code](https://github.com/jaiment/claude-cowork-design-code-lessons-learned/blob/main/docs/CLAUDE_md_for_Code.md)

After which I worked through the next prompt to make a clean handoff so the next chat could pick up cold.

Other source-of-truth files are:

* [Design playbook](https://github.com/jaiment/claude-cowork-design-code-lessons-learned/blob/main/docs/CLAUDE_DESIGN_PLAYBOOK.md) ← more on this in a bit
* Slug/URL inventory
* SEO inventory
* SEO intelligence report ← Done with Fable, why not 🤷‍♂️
* Brand guide
* Brand assets index
* Content inventory
* Post launch backlog

All these files shared memory the agents didn't have on their own.

## The most dangerous pass: Design to Code

The riskiest moment in the whole project was the handoff from Design to Code: taking the approved ZIP and porting it into the real codebase. This is where "modernize" becomes drift. If Code quietly rewrites the design's inline styles into its own cleaner conventions, the pixels shift, and the UI breaks. So the rule was hard: fidelity over refactor. Port the visual output exactly. The only thing Code was allowed to translate was the prototype's runtime globals, the mock router, into real Next.js modules.

I didn't trust this pass once, so I ran three audits BEFORE Code ever touched the handoff bundle:

1. **Cowork auditing its own prompt.** Inside the same conversation where we planned the port, I made Cowork red-team the prompt it had just written, against the design bundle and the repo.
2. **A second Cowork, in a fresh chat.** A clean chat with zero context, told to assume nothing and verify **the plan** against the real files. It found a gap that would have broken a later phase and several wrong references. ([the prompt](https://github.com/jaiment/claude-cowork-design-code-lessons-learned/blob/main/prompts/PROMPT_pre-handoff_audit_foundations.md))
3. **A third Cowork, cross-model with Fable.** A final red-team on the **actual prompt**, explicitly told the plan had already been audited twice and to trust none of it. It caught instructions that contradicted the real assets and a couple of dead references. ([the prompt](https://github.com/jaiment/claude-cowork-design-code-lessons-learned/blob/main/prompts/PROMPT_pre-handoff_audit_C2_crossmodel.md))

Every one of those passes found real things to fix. The handoff between two AI agents is exactly where errors hide, because both of them sound overconfident. Lesson learned: An engineer trusting too much in the confidence of their agents ← not a good idea.

## Cowork 101 fails (LOL)

For Cowork, I used Opus 4.8 most of the time. From my experience, it relies heavily on its training rather than looking up real, up-to-date info from the web. So, the first fail was that Cowork suggested my dev stack in session 1 (too early from my point of view but OK I was just giving it a try) and told me to use **Node.js 20**, which was already end-of-life (EOL was April 30, 2026, and I started in June). I did not catch this flop soon enough and, by the time we were cross-checking to deploy to AWS Amplify, you might imagine what happened. That is NOT a small detail, it's a STRATEGIC decision, and Cowork should NOT have trusted its training and should have double-checked on the web.

Second fail: making prompts for Claude Design, a tool it had NO specific training on. I realized this when it almost broke all the progress made on Design, duplicating templates whose originals now appeared blank ... and I couldn't figure out what was happening. So I forced Cowork to search the internet and become an expert in Claude Design ON EVERY NEW CHAT: learn the official docs, search for known bugs, etc. After that learning, THEN I could actually trust the prompts Cowork wrote.

And a pattern spread all over: I had to stay on top of Cowork on every decision, every direction and every next step it proposed. I re-routed it in every single session. ← My expectation was a lot higher with Opus 4.8 ... never mind, picture clear.

## Design playbook

Straight from the official docs, the single most useful thing I handed to Cowork was → a good design prompt needs four things: goal, layout, content and audience.

→ [Full Design playbook in the repo](https://github.com/jaiment/claude-cowork-design-code-lessons-learned/blob/main/docs/CLAUDE_DESIGN_PLAYBOOK.md).

* **Be specific with feedback.** "It doesn't look good" gets you nowhere. "Tighten the spacing between fields to 8px" gets you the fix.
* **Name components by their real name.** Those names carry into the handoff to Code, so "use the Primary Button" beats "make it a button."
* **Start simple, add complexity in layers.** Design responds far better to incremental requests than to one giant prompt.
* **Think responsive from the first prompt.** Declare mobile, tablet and desktop up front instead of bolting it on later.
* **Ask for edge cases before the handoff.** Empty, error and loading states, plus different data volumes. Much easier to catch in Design than in Code.

## Cowork rules

→ [Full Cowork project instructions in the repo](https://github.com/jaiment/claude-cowork-design-code-lessons-learned/blob/main/docs/COWORK_PROJECT_INSTRUCTIONS.md).

* **Verify before you prompt or claim.** Read the real file (the current bundle version or the repo) and confirm every name, path, line and slot exists exactly as written before citing it. Never invent names.
* **Diff against the right baseline.** When auditing a snapshot, compare the new version against the correct previous one (vN vs vN-1), not against memory.
* **Perception honesty.** If I can't read a detail in a screenshot, I say so. I don't guess.

## The stack

* **Next.js 14** (App Router) + **TypeScript**, with **next-intl** for bilingual routing.
* **Tailwind** for layout only.
* **lucide-react** for icons.
* **AWS Amplify Hosting** in SSR mode. ← best for SEO purposes.
* Deploy pipeline: **GitLab → AWS CodeCommit → Amplify**.
* **CloudFlare** for DNS in front of it.

## Human skills

If I can highlight one single thing from the story, it's this one: I was able to get this project done in under a month **not because** the three agents collaborated remarkably or because they understood the scope in a superb way ← the agents are FAR from this.

I'm **no** expert in website design, or backend dev, or SEO, or DevOps. I can't even tell at a glance if an HTML document is well formatted. What I do have is the fundamentals and a clear picture of WHY these pieces of the puzzle exist and HOW they should collaborate. PLUS I have a clear understanding of the business goals, what problems we solve for our clients and what image we want to project to potential customers.

Forget about specific names: Cowork, Opus, Sonnet, Nova, Kimi ← all these will pass and new ones will come. What matters is to solve real problems and get shit done with higher quality every day ... isn't it 🤷‍♂️

## Grab the templates

I published the redacted, generic versions of the actual working docs so you can copy the structure instead of inventing it: **[claude-cowork-design-code-lessons-learned](https://github.com/jaiment/claude-cowork-design-code-lessons-learned)**.

Inside:

* [Cowork project instructions](https://github.com/jaiment/claude-cowork-design-code-lessons-learned/blob/main/docs/COWORK_PROJECT_INSTRUCTIONS.md) — the orchestrator's operating rules and the three-chat workflow.
* [Design playbook](https://github.com/jaiment/claude-cowork-design-code-lessons-learned/blob/main/docs/CLAUDE_DESIGN_PLAYBOOK.md) — how to drive Claude Design without losing your work.
* [CLAUDE.md for Code](https://github.com/jaiment/claude-cowork-design-code-lessons-learned/blob/main/docs/CLAUDE_md_for_Code.md) — the brain that keeps Code faithful to the design.
* The two pre-handoff red-team prompts: [foundations audit](https://github.com/jaiment/claude-cowork-design-code-lessons-learned/blob/main/prompts/PROMPT_pre-handoff_audit_foundations.md) and [cross-model audit](https://github.com/jaiment/claude-cowork-design-code-lessons-learned/blob/main/prompts/PROMPT_pre-handoff_audit_C2_crossmodel.md).

---

*Disclosure: I currently work at [Factor BI](https://www.factorbi.com), where we build Amazon QuickSight dashboards, data pipelines and Claude-based agents on top of these technologies. This piece is about a lived experience, not a product pitch. Views are my own.*
