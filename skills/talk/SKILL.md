---
name: talk
description: "Turn a rough idea into an evidence-grounded decision through dialogue. Surfaces all hidden assumptions before they become code. Use for planning, architecture choices, scope decisions."
---

# Talk: Evidence-Grounded Decision Through Dialogue

Turn rough idea → grounded recommendation + all visible assumptions.

**Multi-turn process**: Apply method throughout discussion. Each response should re-ground in evidence, re-check assumptions, and continue applying first principles—not just the initial answer.

## Why this matters

Rot starts when you meet an unknown, pick a reasonable default to keep moving, and that default silently becomes fact. It gets coded, tested (proving consistency, never correctness), and built upon. When reality disagrees, the only affordable fix is a patch.

Every step of that chain is cheap to stop at step one, while the assumption is still one sentence. **Any hidden assumption can affect the overall result.** That's what `talk` does: surface all assumptions before they become code.

## Language: Write like a colleague at a whiteboard

Clear, direct language is not optional—it's how the user can actually check your reasoning.

**Core rules** (inspired by ASD-STE100 simplified technical English):
- **Short sentences**: one idea per sentence, 20-25 words max
- **Common words over jargon**: if a term is needed, say what it means the first time
- **Active voice**: "I read X and found Y", not "X was examined"
- **Concrete over abstract**: show observable behavior, not categories

**Why this matters**: some models output jargon that sounds smart but hides unclear thinking. The user needs to understand fast enough to say "yes" or "no, actually..." in one glance.

**Test**: if the user would need to ask "what does that mean?", rewrite it.

## Method

**First principles thinking** → cut to load-bearing decision → ground in evidence → surface ALL assumptions → ask about them → pause for discussion.

### 1. Restate understanding

Before you proceed, restate what you heard in your own words:

"You want to [X] because [Y]. The core question is [Z]."

Let the user say "yes" or "no, actually I mean [W]". Wrong understanding is cheap to fix now, expensive after you've built on it.

### 2. Cut to Core

Apply **first principles**: what's the actual problem? One load-bearing decision usually determines others.

**Independence test**: can decisions be made separately without creating contradictions?
- If yes (multiple independent decisions): stop, tell the user these are separate decisions that should be made independently
- If no (one determines others): proceed with load-bearing decision

### 3. Ground in Evidence

Read: governing docs, prior decisions (check `docs/adr/`), current behavior, relevant code.

**Critical thinking**: distinguish fact from inference. Cite `path:line` or source.

**When evidence missing** (greenfield/new domain):
- State explicitly what doesn't exist yet
- Ground in: user constraints, external contracts, proven patterns for similar problems
- Mark all reasoning as `[inferred from analogy]`

### 4. Recommend One Direction

**Occam's razor**: simplest approach that solves, grounded in evidence.

Structure (compact):
- **Decision**: one line what + why
- **Rationale**: decisive evidence (cited) — include when non-trivial
- **Boundary**: what's in/out — include when scope ambiguous
- **Risk**: most likely failure mode — include when non-obvious

Mention alternative only if tradeoff genuinely close.

### 5. Surface ALL Assumptions

**Mark explicitly**, never bury. Any hidden assumption can affect the result.

List every assumption this recommendation depends on:
- **Confirmed**: user stated outcome/constraints (fixed)
- **Assumed**: choice you made to proceed, user never confirmed — write as observable behavior: "After delete, past orders stay visible to admins for 30 days"
- **Open**: answer would change direction, still unknown (+ what it blocks)
- **Unexamined**: dimension user hasn't raised (name the class: "operational costs", "compliance", "failure recovery")

**Assumption hygiene**: 
- Assumed/Unexamined is where decisions rot
- Implementation proves consistency, never correctness
- Surface while still one sentence to change

**Classification boundary**:
- Confirmed vs Assumed: if user said "I want it fast" → speed is confirmed; what "fast" means numerically is assumed
- Assumed vs Open: assumed has plausible default you picked; open has no defensible default yet
- Open vs Unexamined: open is specific (performance target); unexamined is class (have you considered operational costs?)

### 6. Ask & Pause

Ask about **all the load-bearing assumptions** (those most likely to change the direction). Not just one—any hidden assumption can matter.

Output recommendation with all assumptions stated.

**Stop and wait for discussion**. User may:
- Answer the assumptions → refine recommendation
- Use `/grill` for systematic exploration of decision space
- Request file output after discussion converges

## Output Format

Single unified format (scale complexity as needed):

```
**My understanding**: <restate core problem in your own words>

**Decision**: <what + why>
[**Rationale**: <evidence cited> — include when non-trivial]
[**Boundary**: <in/out> — include when scope ambiguous]
[**Risk**: <failure mode> — include when non-obvious]

**Assumptions** (any hidden one can affect the result):
- Confirmed: <what user stated>
- Assumed: <defaults I picked, as observable behavior>
- Open: <undecided> → blocks: <what>
- Unexamined: <dimensions not yet considered>

**Key questions**:
<Ask about all load-bearing assumptions, not just one>
```

**[...] means optional** - include sections only when they add value. Simple decisions may only need Understanding + Decision + Assumptions + Questions.

**Critical**: Always end pausing for user input. Let the user decide whether to dig deeper—don't prescribe what you'll do next.

Do not write file unless explicitly requested. This is a discussion format.

## Multi-turn discipline

The skill text is read once and fades. What keeps the loop alive is **what you wrote last round**. So every response, round 1 or round 7:

- Starts with understanding (round 1) or what changed (round 2+)
- Cites evidence you read this round
- Shows all assumptions for current recommendation
- Asks about load-bearing assumptions
- Ends with a stop

**Round 2+ additions**:
- Move answered items in the assumption list (Assumed → Confirmed if user agreed)
- **Ablation**: when an answer changes an assumption, check what depended on it. Can you drop complexity you added earlier?
- Surface new assumptions the user's answer revealed

## Discipline

- **High cohesion, low coupling**: group related decisions, separate independents
- **Ablation thinking**: can we drop this complexity? What breaks?
- **Independent thinking**: check evidence yourself, don't anchor on user's proposed mechanism
- **Occam's razor**: simplest that works
- Official solutions before custom design
- For hard problems: study proven implementations, name adopted mechanism

## Gates

- Contradiction with project rules/approved decisions: resolve before proceeding
- External dependencies/irreversible effects: explicit
- Blocking ambiguity: stays Open (don't fake closure)
- No unlabelled assumption reaches recommendation
- Any hidden assumption can affect the result—surface all of them

## Boundary with /grill

**talk**: finds all assumptions **this recommendation** depends on
- "What needs to be true for this approach to work?"
- Output: recommendation + complete assumption list

**grill**: systematically explores **the entire decision space**
- "What angles haven't I considered?"
- Output: comprehensive question tree across all dimensions

If the user wants "did I miss anything?", that's `/grill`. If they want "is this approach sound?", that's `talk`.

## File Output (Optional)

**Only when user explicitly requests**: "输出到文件" / "write decision record" / "写 ADR" / provides path.

Output is an ADR. Read `references/ADR-FORMAT.md` and follow it: location, numbering, template.

Key points:
- Location: `docs/adr/NNNN-slug.md` (increment from highest existing number)
- If `CONTEXT-MAP.md` exists: context-specific decisions go in that context's `docs/adr/`
- Use project's terms from `CONTEXT.md` if it exists
- If this replaces an existing ADR: mark old one `superseded by ADR-NNNN`
- If this settles a `proposed` ADR's assumptions: update that to `accepted` instead of duplicating

**Ledger → ADR mapping**:
- Only Confirmed content is written as the decision
- Assumed or Open items the decision depends on → Consequences section ("Assumes X; if wrong, Y changes"), set `status: proposed`
- Low-stakes defaults don't go in the ADR
- Constraints not visible in code (legal, compliance, contracts) → one sentence of context

Check before finishing: nothing Assumed reads as settled, the "why" cites the real reason from conversation.
