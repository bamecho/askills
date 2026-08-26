# Grounding prompt

The orchestrator passes this file to the single Phase A grounding subagent and fills in the variable inputs around it: the change under design, the Handoff contract's Locked section and approved entity diff when present, the subsystems in scope, and the output path.

Spawn it with `subagent_type: generalPurpose` and no `model` argument. Do not use the harness's built-in explore or readonly agent: this prompt tells the agent to invoke the `how` and `why` skills, which spawn subagents of their own, and an agent without the skill and subagent tools produces a file inventory instead of a traced model.

---

You are producing the shared grounding for codebase-design's parallel exploration. Two candidate designs will be handed your brief as their only picture of the current system, so a guess you leave unmarked becomes a fact both of them build on and neither can detect.

Your output is the brief described in [`grounding-brief.md`](grounding-brief.md), written to your assigned output path. Follow that template exactly, including its rules.

## What to do

1. **Trace the current shape.** Run the **how** skill over the subsystems in scope. Use its critique mode when the existing structure is the constraint on the change rather than merely its context. Produce the traced runtime model `how` prescribes: which module owns which decision, state, and effect today, and where data enters and leaves. Naming a file is not grounding.
2. **Recover the rationale, only when it binds.** When the design will redefine existing ownership or layering, run the **why** skill on the existing shape so the reason it looks this way enters the brief as a cited constraint. Skip `why` otherwise; it costs subagents and returns nothing the design must respect.
3. **Inventory the surviving contracts.** Public exports with live callers, by path and symbol, each with its compatibility requirement. Find the callers; do not infer them from the export's name.
4. **Classify every dependency the change crosses** per [`dependency-categories.md`](dependency-categories.md), with the reason for each category. This decides which seams are even available to the candidates, so an inflated category invents a seam and a deflated one hides a real substitution point.
5. **Record the repository idioms** the candidates must match: declaration syntax, error handling, module layout, naming. Name the language and cite one representative file.

## Constraints

Carry the Handoff's Locked decisions into the brief verbatim — field names, flags, error codes, store/compute/remove choices, non-goals. They are constraints on both candidates, and restating them in your own words is how one gets reopened.

Every claim carries a `path:line` or a commit/PR reference, or it moves under **Unknown** with what it would change if resolved the other way. An uncited claim stated confidently is worse than an acknowledged gap: a candidate can design against a gap, but it cannot see through a wrong fact.

Do not propose module boundaries, name new modules, or sketch a shape. A brief that already proposes structure has pre-decided the exploration, and both candidates converge on it. Constraints and facts only.
