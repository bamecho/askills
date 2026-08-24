---
name: think
description: >
  Turn a rough idea into an evidence-grounded product or technical decision. Use for planning direction, scope judgment, architecture choices, feasibility, or value judgment before implementation. Keep requirements and specs minimal; when an idea contains too many independent decisions for one coherent pass, suggest `wayfinder` first. Not for routine bug fixes or evaluating an already-built feature.
---

# Think: Reach a Decision

Prefix your first line with 🥷 inline, not as its own paragraph.

Before taking a position, separate confirmed requirements from known unknowns and consequential assumptions the user has not confirmed. Treat the user's stated outcome and constraints as confirmed; treat proposed mechanisms and agent-invented alternatives as hypotheses. Test a hypothesis against the confirmed constraints before recommending it; when they conflict, reject the proposal and return to the confirmed outcome instead of patching the conflict with extra rules. Do not turn the reply into a questionnaire: surface the one assumption most likely to change the direction and leave lower-impact details to later design. Implementation and passing tests cannot validate an assumption. `think` owns the recommendation: what problem is worth solving, which approach best fits the evidence, and where its meaningful boundary lies.

## Contract

- **Outcome:** one evidence-grounded solution to the product or technical decision the user actually faces.
- **Done when:** the chosen direction, decisive evidence, meaningful boundary, and fragile premise are explicit enough for the user to judge whether to adopt the solution. When a spec is warranted, its Acceptance, non-goals, and real Open blockers are explicit too.
- **Evidence:** current repository state, governing project docs, live external contracts when relevant, prior decisions, and user constraints.
- **Output:** the shortest useful answer in chat. Write a Minimal Spec only when the user requests one or the decision needs a durable multi-part contract.

Decision-complete means the user can choose a direction, not that every feature has been designed. Every included detail must change the recommendation, its acceptance, or a real blocker. Keep the requirement or Minimal Spec to the smallest coherent surface.

## Default Workflow

1. Confirm the real repository path. Read governing instructions, matching prior decisions, current behavior, callers, and configuration values that bear on the decision. When durable project context exists, use [references/durable-context.md](references/durable-context.md) for its read order and re-verification rules.
2. Keep the decision minimal. If a useful Minimal Spec still depends on multiple unresolved product decisions, stop and suggest `wayfinder` first; do not choose a subset or draft the spec. Otherwise, frame the smallest decision that preserves the observable target and defer mechanics that do not change its direction. Treat scope as the boundary of the recommendation; add sequencing only when requested or when it changes the choice.
3. Recommend one approach with rationale, effort, risk, and the most fragile premise. Mention one alternative only when its tradeoff is genuinely close.
4. When a spec is warranted, lock only decisions supported by the user or evidence. Put missing choices that block the spec under Open with impact, default if honest, and owner.
5. Present the recommendation in chat. One sentence is enough when it carries the decision, boundary, and fragile premise. Make the approval boundary explicit only when further work depends on acceptance.

Return the recommendation itself. Omit skill, workflow, and routing narration.

Use official framework or service solutions before custom design. Query current official documentation when an external contract affects the choice. For a hard or repeatedly failed problem, study proven implementations and name the mechanism adopted.

### Output Surface

Reply in chat by default. Adapt the shape to the decision: a one-line answer, short bullets, or a compact review surface. File creation activates only when the user explicitly asks to export/write or provides an output path; “输出到文件” is sufficient authority.

## Review Surface

When a spec is warranted, use only the fields needed to review the decision: Target, In/Out scope, observable Acceptance, Recommendation, fragile premise, and real Open blockers. Reference upstream facts by path and omit empty sections.

## Gates

- A contradiction with `AGENTS.md`, `CLAUDE.md`, project rules, or an approved decision is named and resolved before approval.
- External dependencies, credentials, migrations, concurrency, or irreversible effects that shape Scope or Acceptance are explicit.
- More than three interacting components get one small ASCII dependency/sequence diagram; otherwise prose and contracts are cheaper.
- A blocking ambiguity stays Open. Reversible implementation choices remain for the executor.

## Ownership Boundary

- A spec locks only concrete decisions supported by the user or evidence; unresolved blockers remain Open with impact and owner.
- Implementation details stay out unless they materially change the choice.
- Unsupported product tokens remain Open rather than becoming settled facts.
- A capability the user defers stays outside current Scope; express validation as observable evidence without silently adding that capability.
- Investigation resolves uncertainty before recommendation.
