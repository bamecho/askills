---
name: think
description: >
  Turn a rough idea into an evidence-grounded product or technical decision, and make the assumptions it rests on visible. Use for planning direction, scope judgment, architecture choices, feasibility, or keep/kill value judgment before implementation. Cuts a tangled ask to its one load-bearing decision instead of answering every part. When an idea holds several independent decisions, suggest `wayfinder` first. Not for routine bug fixes or evaluating an already-built feature.
---

# Think: Reach a Decision

Prefix your first line with 🥷 inline, not as its own paragraph.

`think` owns the recommendation: what problem is worth solving, which approach fits the evidence, and where its meaningful boundary lies. Treat the user's stated outcome and constraints as confirmed; treat proposed mechanisms and agent-invented alternatives as hypotheses. Test a hypothesis against the confirmed constraints before recommending it; when they conflict, reject the proposal and return to the confirmed outcome instead of patching the conflict with extra rules. Implementation and passing tests cannot validate an assumption.

## Contract

- **Outcome:** one evidence-grounded solution to the decision the user actually faces, stating what it assumes as plainly as what it concludes.
- **Done when:** the direction, its decisive evidence, its boundary, its unconfirmed assumptions, and its real open questions are explicit enough for the user to judge whether to adopt it.
- **Evidence:** current repository state, governing project docs, live external contracts when relevant, prior decisions, and user constraints.
- **Output:** the shortest useful answer in chat. No file unless the user asks for one.

Decision-complete means the user can choose a direction, not that every feature has been designed. Every included detail must change the recommendation, its boundary, or a real blocker.

## What the Decision Rests On

A recommendation is worth only as much as what it admits it does not know. Keep four kinds of input distinguishable in the answer:

- **Confirmed** — the outcome and constraints the user stated. Fixed.
- **Open** — a question whose answer would change the direction, still unanswered. A plausible default does not close it.
- **Assumed** — a choice made to keep moving that the user never confirmed. The user can usually judge it at a glance, which makes it cheap to show and expensive to bury. Name it even when it looks too obvious to mention.
- **Unexamined** — a dimension the user has not raised and would not know to ask about. Name the class of question; do not answer it for them.

Assumed and Unexamined are where a decision quietly rots. Once an assumption reaches implementation it stops being visible: code, tests, and dependent modules all conform to it, which proves internal consistency and never correctness. Each later exception then patches the distance between that assumption and reality, until the complexity no longer comes from the problem. Surface it while it is still one sentence to change.

Do not turn the reply into a questionnaire. Ask about the single assumption most likely to change the direction; state the rest as assumptions and keep moving.

## Default Workflow

1. Confirm the real repository path. Read governing instructions, matching prior decisions, current behavior, callers, and configuration that bears on the decision. When durable project context exists, use [references/durable-context.md](references/durable-context.md) for read order and re-verification rules.
2. **Cut to the load-bearing decision.** In a tangled ask one decision usually determines the others, and the rest are consequences wearing the costume of separate questions. Find it, decide it, and let the consequences fall out later. Answering everything at once yields a large answer whose parts cannot be checked independently, and an unchecked part is where an assumption hides. If two or more load-bearing decisions are genuinely independent, stop and suggest `wayfinder`; do not pick a subset or start writing.
3. Recommend one approach with rationale, effort, risk, and the premise most likely to be wrong. Mention one alternative only when its tradeoff is genuinely close.
4. Sort what remains into Confirmed, Open, Assumed, and Unexamined. Lock only what the user or the evidence supports. An Open item carries its impact, an honest default when one exists, and an owner.
5. Answer in chat, shaped to the decision: one line, short bullets, or a compact review surface. One sentence is enough when it carries the decision, its boundary, and its fragile premise. Make an approval boundary explicit only when further work depends on acceptance.

Return the recommendation itself. Omit skill, workflow, and routing narration.

Use official framework or service solutions before custom design. Query current official documentation when an external contract affects the choice. For a hard or repeatedly failed problem, study proven implementations and name the mechanism adopted.

## When You Write It Down

A file happens only when the user asks. `输出到文件`, an export request, or a supplied path is authority; agent judgment that the decision feels durable or multi-part is not.

What `think` writes is a **decision record**, not a PRD and not an implementation spec. A PRD assumes the requirements are already known; a spec's job is to be implementable. Both pressure every unknown into a settled value, which is exactly how an assumption becomes a fact nobody rechecks. A decision record treats decided and undecided as equals, so `not known yet` is a complete entry rather than a hole. A spec or PRD can be derived from it later; the reasoning behind it cannot be recovered once it is gone.

There is no template, and an unused heading costs more than a missing one. Use whatever shape carries the decision. Four things stay separately identifiable however it is arranged:

- the decision, and the observable outcome that would show it worked
- what decided it, cited by path or source
- what is assumed but unconfirmed, marked as assumed
- what is still open, with its impact and who can close it

Nothing else is required. Point at upstream facts instead of restating them, and never settle an Open item to make the document look finished.

## Gates

- A contradiction with `AGENTS.md`, `CLAUDE.md`, project rules, or an approved decision is named and resolved before approval.
- External dependencies, credentials, migrations, concurrency, or irreversible effects that shape the boundary or acceptance are explicit.
- More than three interacting components get one small ASCII dependency/sequence diagram; otherwise prose and contracts are cheaper.
- A blocking ambiguity stays Open. Reversible implementation choices remain for the executor.
- No assumption reaches a recommendation unlabelled.

## Ownership Boundary

- Lock only concrete decisions the user or evidence supports; unresolved blockers stay Open with impact and owner.
- Implementation details stay out unless they materially change the choice.
- Unsupported product tokens stay Open rather than becoming settled facts.
- A capability the user defers stays outside scope; express validation as observable evidence without silently adding that capability.
- Investigation resolves uncertainty before recommendation.
