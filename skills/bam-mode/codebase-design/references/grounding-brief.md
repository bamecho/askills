# Grounding brief

Phase A's output. The grounding subagent writes this file per [`grounding-prompt.md`](grounding-prompt.md); the orchestrator reads it, then hands its path to every Phase B candidate. It is the shared constraint set; candidates diverge on structure, never on facts.

Target ≤2 pages. Facts with citations, not narrative. Every claim carries a `path:line` or a commit/PR reference, or it moves under **Unknown**.

```md
# Grounding: <slice name>

## The change
<Two or three sentences. What the slice must make possible, stated as an
observable outcome. Copied from the approved spec or Handoff, not re-derived.>

## Locked decisions
<From the Handoff contract's Locked section. Field names, flags, error codes,
store/compute/remove choices, non-goals. Candidates may not reopen these.>

## Current shape (from `how`)
<The traced runtime model of the subsystems the slice touches. Which module owns
which decision, state, and effect today. Where the data enters and leaves. Cite
`path:line` for each owner. A file inventory is not a runtime model.>

## Existing contracts that must survive
<Public exports with live callers, by path and symbol, with the compatibility
requirement for each. Preserve unless the Handoff explicitly changes it.>

## Dependencies the slice crosses
<One line per dependency with its category from
[`dependency-categories.md`](dependency-categories.md), and why. This decides
which seams are even available to the candidates.>

## Rationale that constrains the design (from `why`)
<Only when the design redefines ownership or layering. Why the current shape is
the way it is, cited to PR/ticket/commit/incident. Distinguish evidence from
inference the way `why` requires. A rationale you cannot cite is not a
constraint.>

## Repository idioms
<Declaration syntax, error handling, module layout, and naming conventions the
candidates must match. Name the language. Cite one representative file.>

## Unknown
<What Phase A could not establish, and what each gap would change if resolved
the other way. Candidates state an assumption against these rather than
inventing a fact.>
```

## Rules

- **Do not sketch a shape here.** A grounding brief that already proposes module boundaries has pre-decided Phase B, and both candidates will converge on it. Constraints only.
- **`how` before `why`.** Run `why` only when the design redefines existing ownership or layering. Otherwise it costs a subagent and returns nothing that binds the design.
- **Uncited claims are Unknown.** A confident guess in the grounding brief propagates into both candidates and survives synthesis, because neither candidate can see it is a guess.
