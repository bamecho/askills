---
name: codebase-design
description: >
  Design codebase shape before implementation: ground the change in the real current system, explore two structurally distinct shapes in parallel, then present one design document covering module ownership, dependency direction, seam placement, and exact load-bearing contracts. Use when ownership, public interfaces, seams, or caller knowledge change. Skip when one owner and one interaction are already obvious. Not for implementation, rollout ordering, or repository-wide architecture review.
---

# Codebase Design

Design the shape, then hand it over. This skill produces one design document and stops; it does not write implementation code.

Establish the whole system shape before local declarations: who calls, which module owns each decision, state, and effect, and how critical interactions cross the seams. Then define only the load-bearing contracts implementation must not silently redesign.

Three things make this work, and skipping any one collapses it into a guess: the design is grounded in the *real* current system, it is designed twice in parallel before anything is chosen, and it stops for approval before code exists.

Read [`references/design-vocabulary.md`](references/design-vocabulary.md) first. Its terms — module, interface, depth, seam, adapter, leverage, locality — are how the two candidates stay comparable.

Open a todolist with one entry per phase. The middle phases run subagents, and the list is what keeps a phase from silently disappearing.

## Phase A: Ground

Build a real model of every system the change touches. Naming a file is not grounding.

Delegate grounding to one general-purpose subagent — not the harness's built-in explore or readonly agent — and keep the orchestrator's context for Phase C, where it must read both candidates end to end. Pass [`references/grounding-prompt.md`](references/grounding-prompt.md) as its prompt; that file carries the agent type and why, the `how` and `why` sequencing, and the citation discipline.

It writes `/tmp/codebase-design-<slug>/grounding.md` per [`references/grounding-brief.md`](references/grounding-brief.md) and returns a summary. Read the file, not the summary — the file is what both candidates receive. Move every uncited claim under **Unknown** first: you did not do the reading, so a guess looks identical to a traced fact, and both candidates will build on it.

Skip Phase A for greenfield work with no surrounding system. When the change touches one subsystem already traced in this conversation, pass the returned summary inline; a three-line grounding does not earn a file.

## Phase B: Design twice

Run the **arena** skill with the structure-design task and the grounding brief path. Pass [`references/candidate-prompt.md`](references/candidate-prompt.md) as each candidate's prompt.

Spawn exactly two candidates. Pass this to arena as a hard candidate count. Never more than two subagents at once. Do not pass a `model` argument. Arena's cross-judge is a separate readonly reviewer, not a third candidate.

Give each candidate a different structural constraint so the two explore genuinely different shapes rather than two flavors of one. The candidate prompt carries the default pair and the substitutions worth making when the grounding brief points at a different axis; name the axis before spawning.

Require two structurally distinct shapes before synthesis, even when the first looks sufficient. Whole-shape alternatives, not point fixes inside one shape.

## Phase C: Synthesize

Screen both candidates against [`references/shape-review.md`](references/shape-review.md) and revise or reject what it flags. Compare on interface depth: prefer the shape hiding more complexity behind a smaller public surface. Do not mistake a deep call chain for a deep module.

Pick the base on which shape a future maintainer can extend without breaking invariants, then graft what the other got right, by hand — the result must hold under one mental model. When both converge, note it and ship the consensus shape.

Screen the synthesized shape again; grafting can reintroduce a flag neither candidate had. Reconcile every interaction label in the module map against the declarations: a flow cannot return, accept, or observe a value its contract does not provide. Do not invent concurrency, locking, isolation, batching, or retry policy without approved evidence.

## Phase D: Present

Write one design document per [`references/design-document.md`](references/design-document.md) to **`docs/design/NN-<slug>.md`**, in the user's language, and stop for approval. That document is the deliverable and the contract implementation is handed. Report concrete decisions, not rubric compliance.

`NN` is the next unused two-digit number in `docs/design/`, so the directory listing reads in the order the designs were made — which is the order they constrain each other. Use the same `<slug>` as the Phase A working directory, so the transport and the deliverable are visibly one run.

The two locations differ in durability, not convenience: `/tmp/codebase-design-<slug>/` holds the grounding brief and both candidate proposals, which exist only because subagents cannot share context and are disposable once Phase C has read them. `docs/design/NN-<slug>.md` is what implementation is handed and what a reader returns to months later, so it belongs in the repo and in review.

Then stop. Do not implement, order rollout steps, or open tickets. Human pushback on the shape is Phase A evidence: re-ground and re-run Phase B rather than patching the rejected shape.

When one owner, dependency direction, and interaction are already obvious, write `codebase-design skipped: <reason>` instead of running the phases.

## Stage Boundary

Consume the locked decisions the grounding brief carries; do not reopen product tokens or invent product fields. Only a decision the user or upstream evidence has confirmed is locked — an assumed or open one stays under **Unknown**, and a design that needs it resolved names it as a blocker rather than picking a value. Planning may order the approved contracts but does not redesign them; implementation owns bodies and diff evidence.

Routing cases for this boundary live in `evals/trigger_cases.json`.
