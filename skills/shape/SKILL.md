---
name: shape
description: "Design codebase structure before implementation. Use when ownership, interfaces, or module boundaries need definition. Skip when structure is obvious."
---

# Shape: Design Codebase Structure

Design structure before code. Output: one design doc in `docs/design/`. No implementation.

## When to use

Use when:
- Module ownership is unclear
- Interface boundaries need definition  
- Multiple ways exist to split responsibility
- Change crosses existing module boundaries

Skip when:
- One obvious owner and interface exist
- Change stays inside one module
- Structure is already agreed

## Design principles

1. **Group what changes together** — Put related code in one place. Keep unrelated code separate.
2. **Hide complexity** — Small public interface that hides complex capability.
3. **Simplest that works** — Add complexity only when proven necessary.
4. **One owner per thing** — Every piece of state has one clear owner.
5. **Design data first** — Design data structures, then operations that use them.
6. **Track decisions** — Design doc cites real code (`path:line`). Future code tracks back to design.

## Method

### 1. Trace current system

Use explorer agent to read current code. Pass this task:

"Trace current system for [change].

Find:
- Which module owns what (cite path:line)
- How data flows (enters → processes → leaves)
- Who calls the public APIs
- What dependencies exist

Cite everything or say unknown."

Read the agent output. Check every claim is cited.

Skip for new code: write 3-line summary instead.

### 2. Explore two designs

Use two agents with different constraints.

Pick constraints based on step 1:
- Default: minimize interface vs optimize for main caller
- When ownership unclear: keep boundary vs merge ownership
- When many dependencies: port locally vs adapt at edge

Pass each agent:

"Design structure for [change]. Use constraint: [specific constraint]. Use grounding from step 1.

Apply:
- Group what changes together
- Hide complexity behind small interface
- Design data structures first
- One owner per thing
- Simplest that works

Return:
- Where code calls this (2-3 real sites)
- Module map (ASCII diagram, show state/effects)
- Key function signatures (no bodies)
- Adapter placement (if adding boundaries)
- Why this structure works, what you rejected

Follow your constraint. Make it different from the other design."

Read both outputs.

### 3. Synthesize

Read both designs from step 2.

Compare how well each hides complexity. Pick the base (can extend without breaking). Take what the other got right.

Check for problems:
- Interface exposes too much coordination → make it smaller
- Shared representation across boundary → translate at edges
- Pass-through (forwards without policy) → merge if no real divergence
- Hypothetical adapter (one adapter, might need more) → merge until actually varies

If problems found: revise or re-run step 2 with adjusted constraint.

### 4. Write design doc

Write to `docs/design/NN-[slug].md` (user's language). NN = next unused number.

Include:
- **Decision**: what structure, why (2-3 lines)
- **Caller usage**: how it looks from sites where code calls
- **Module map**: visual structure (ASCII)
- **Key contracts**: function signatures that matter
- **Why this structure**: base reasoning, what taken from other design, what rejected
- **Preserved contracts**: existing `path:symbol` kept for compatibility (or "none")
- **Tradeoffs**: accept X for Y (or "none identified")
- **Blockers**: unknowns that remain (or "none")

Cite real code. No implementation details.

**Then stop.** No code, no tickets.

## Topics: what makes good structure

| Topic | Principle |
|---|---|
| Module Boundary | One clear responsibility. Minimal public surface. |
| Interface Depth | Hide complexity. Rich capability through narrow entry. |
| Ownership | Every state/behavior has one owner. No shared coordination. |
| State Flow | Stateless when possible. Mutable state justifies lifecycle cost. |
| Seam Placement | Add boundary only when responsibility splits. Not for file size. |
| Composition | Build from focused pieces. Each does one thing well. |
| Traceability | Design doc → code. Code → design doc. Both cite each other. |

## If user disagrees

- Facts wrong → re-run step 1 (grounding)
- Constraint choice wrong → re-run step 2 with different pair
- Synthesis logic wrong → revise step 3 with user guidance
- Presentation only → edit doc directly
