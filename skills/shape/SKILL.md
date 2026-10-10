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

1. **High Cohesion, Low Coupling** — Group what changes together. Separate what changes independently.
2. **Interface Depth** — Narrow surface that hides complex capability. This is better than wide shallow surface.
3. **Occam's Razor** — Use simplest structure that solves. Add complexity only when proven necessary.
4. **Explicit Ownership** — Every piece of state and behavior has one clear owner.
5. **Data First** — Design data structures. Then design operations that use them.
6. **Traceability** — Design doc cites real code (`path:line`). Future code tracks back to design.

## Method

### 1. Ground in real system

Spawn subagent to trace current system. Pass task:

"Trace current system for [change description].

Return:
- Current module ownership (cite `path:line`)
- Data flow: enters → processes → leaves
- Public contracts with live callers
- Dependencies crossed
- Repository conventions (cite example file)
- Unknowns: what is missing, what it blocks

Facts only. Cite everything or mark unknown."

Read subagent output directly. Check: every claim is cited or marked unknown.

**Skip for greenfield**: inline 3-line summary instead.

### 2. Explore structures

Spawn 2 subagents with different constraints to explore design space.

Pick pair of constraints based on grounding:
- Default: minimize interface vs optimize for main caller
- When ownership contested: keep boundary vs merge ownership
- When external dependency heavy: port locally vs adapt at edge

Pass each: "Design structure for [change] with constraint: [specific constraint]. Use grounding from step 1.

Apply:
- High cohesion, low coupling
- Interface depth (capability hidden ÷ surface size)
- Data structures first
- Explicit ownership
- Occam's Razor

Return:
1. Caller usage (2-3 real sites where code calls)
2. Module map (ASCII, annotated with state/effects)
3. Key signatures (no bodies)
4. Seam placement (if adding adapters/boundaries)
5. Rationale (why this structure, what rejected)

Follow your constraint honestly. Diverge from other design."

Read both outputs directly.

### 3. Synthesize

Read both designs from step 2.

Compare on interface depth. Pick base (can extend without breaking). Graft what other got right.

Check for problems:
- Shallow interface (surface exposes coordination) → concentrate
- Leakage (shared representation across boundary) → translate at edges
- Pass-through (forwards without policy) → inline if no real divergence
- Hypothetical seam (one adapter, might need more) → inline until actually varies

If problems found: revise or re-run step 2 with adjusted constraint.

### 4. Write design doc

Write to `docs/design/NN-[slug].md` (user's language). NN = next unused number.

Include:
- **Decision**: what structure, why (2-3 lines)
- **Caller usage**: how it looks from sites where code calls
- **Module map**: visual structure (ASCII)
- **Key contracts**: signatures that matter
- **Why this structure**: base reasoning, what grafted from other design, what rejected
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
