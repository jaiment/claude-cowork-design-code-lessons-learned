# 50% technical / 50% human: orchestrating Claude Cowork, Design & Code

I rebuilt a company website in under a month, almost solo, by running three Claude agents as a team: **Cowork** as the orchestrator (the brain), **Claude Design** as the visual lead, and **Claude Code** as the implementation lead.

But the real lesson wasn't technical. The agents each live in a different discipline, and the only reason they worked like one team is that a human coordinator understood *enough* of each: software, visual design, business, analytics and SEO. AI agents don't replace the coordinator. They amplify one. That's the 50% nobody talks about.

This repo shares the redacted, reusable versions of the actual working docs and prompts I used, so you can copy the structure instead of inventing it.

> **Disclosure:** these are lightly redacted copies of real working files. Company/product names, domains, analytics IDs and third-party trademarks are synthetic placeholders. The methodology and the dev stack are shown as-is.

## Read the story first

The full write-up: how the coordination actually worked, the most dangerous pass (Design → Code), where the orchestrator fell short (it once suggested an already end-of-life runtime with full confidence), and why the scarce skill turned out to be human.

→ **[Read the article](article/orchestrating-claude.md)** _(also on [Medium](https://medium.com/@jaiment/50-technical-50-human-orchestrating-claude-design-cowork-code-dedf46c30187))_

## What's inside

**`docs/` — the three "brains"**

- **[Cowork project instructions](docs/COWORK_PROJECT_INSTRUCTIONS.md)** — the orchestrator's operating rules and the three-chat workflow. Generic template.
- **[Design playbook](docs/CLAUDE_DESIGN_PLAYBOOK.md)** — how to drive Claude Design without losing your work: golden rules, gotchas, the surgical-prompt pattern, and the backup discipline.
- **[CLAUDE.md for Code](docs/CLAUDE_md_for_Code.md)** — the brain that keeps Claude Code faithful to the design instead of "modernizing" it into drift. Generic template.

**`prompts/` — the pre-handoff red-team**

- **[Foundations audit](prompts/PROMPT_pre-handoff_audit_foundations.md)** — an independent, fresh-chat red-team of the plan against the real files, before the port.
- **[Cross-model audit](prompts/PROMPT_pre-handoff_audit_C2_crossmodel.md)** — the final adversarial pass on the actual port prompt, run on a different model.

## The one-paragraph method

Three coordinated chats, one direction of flow. Strategy only ever leaves **Cowork**; it drafts every prompt for the two specialists, and everything they produce comes back to it for review. **Design** owns the pixels and exports a bundle. **Code** ports that bundle faithfully and only wires content, strings, routes and logic, never design. The one channel between the specialists is the Design → Code handoff, and that's exactly where errors hide, so it gets red-teamed before it runs. Every session closes with an hour of documentation so the next amnesiac chat can pick up cold. And a human stays on top of all of it.

## License

See [LICENSE](LICENSE). These are templates — adapt them to your own project.
