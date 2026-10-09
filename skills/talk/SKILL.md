---
name: talk
description: "Turn a rough idea into an evidence-grounded decision through dialogue. Surfaces all hidden assumptions before they become code. Use for planning, architecture choices, scope decisions."
---

# Talk: Find the Real Problem, Surface All Assumptions

Turn rough idea → clear decision + visible assumptions.

Apply this method throughout the discussion. Every response should re-ground in evidence, re-check assumptions, continue finding the real problem—not just the first answer.

## Why this matters

Rot starts when you meet an unknown, pick a reasonable default to keep moving, and that default becomes fact. It gets coded, tested, and built upon. When reality disagrees, patches pile up until nobody understands the system.

Stop this at step one, while the assumption is still one sentence. **Any hidden assumption can affect the result.**

## Write like a human talking to another human

Clear language is not optional. The user needs to understand fast enough to say "yes" or "no, actually..." in one glance.

**Rules**:
- One idea per sentence. 20-25 words max.
- Use common words. If you need a technical term, say what it means.
- Say "I read X" not "X was examined".
- Show what the user will see, not abstract categories.

**Test**: if the user would ask "what does that mean?", rewrite it.

Some models hide unclear thinking behind jargon. Don't do that.

## Method

### 1. Say what you heard

Before you do anything else, say what you understood:

"You want [X] because [Y]. The real question is [Z]."

Wait for the user to say "yes" or "no, I mean [W]".

**Why**: you might have misunderstood. Cheap to fix now. Expensive after you build on it.

### 2. Find the real problem

What is the user actually trying to solve?

Look past the mechanism they proposed. Find what problem that mechanism is trying to fix. That's often the real question.

Ask: what's the core issue? What one decision determines the others?

**Test for independence**: can these decisions be made separately without contradicting each other?
- Yes, they're independent → stop. Tell the user these are separate decisions to make independently.
- No, one determines others → that's the real problem. Work on that.

### 3. Read the evidence

Read before you claim anything.

Look at: governing docs, prior decisions in `docs/adr/`, current code, how it behaves now.

**Separate fact from guess**:
- Fact: "The code does X" (cite `path:line`)
- Guess: "This probably means Y" (say it's a guess)

**When evidence doesn't exist** (new project, new domain):
- Say what doesn't exist yet
- Ground in: what the user said, external contracts, patterns that worked for similar problems
- Mark all reasoning `[inferred from analogy]`

### 4. Recommend one direction

Pick the simplest approach that solves the problem. Ground it in evidence.

Say:
- **Decision**: what + why (one line)
- **Rationale**: evidence that led here (cite it) — when non-obvious
- **Boundary**: what's in, what's out — when scope is unclear
- **Risk**: most likely failure mode — when non-obvious

Mention an alternative only if the tradeoff is genuinely close.

### 5. Surface every assumption

List every assumption this recommendation depends on. **Any hidden one can affect the result.**

Mark each one:
- **Confirmed**: user said this (fixed)
- **Assumed**: you picked this default, user never confirmed it — write what the user will see: "After delete, past orders stay visible to admins for 30 days"
- **Open**: you don't know yet, and it would change the direction — say what it blocks
- **Unexamined**: dimension the user hasn't raised — name the class: "operational costs", "compliance", "what happens when it fails"

**Why these matter**:
- Assumed and Unexamined is where decisions rot
- Tests prove consistency, never correctness
- Surface now, while it's still one sentence

**Classification guide**:
- Confirmed vs Assumed: user said "I want it fast" → speed is confirmed; what "fast" means as a number is assumed
- Assumed vs Open: assumed has a plausible default you picked; open has no defensible default
- Open vs Unexamined: open is specific ("what's the response time?"); unexamined is a class ("have you thought about operational costs?")

### 6. Ask about the assumptions

Ask about the assumptions most likely to change the direction. Not just one—**any hidden assumption can matter**.

Show the recommendation with all assumptions stated.

**Stop and wait.** User may:
- Answer the assumptions → you refine the recommendation
- Use `/grill` to explore the decision space systematically
- Ask for a file after discussion converges

## Output format

Scale this to the decision. Simple choices get simple output.

```
**What I understood**: <say the real problem in your own words>

**Decision**: <what + why>
[**Rationale**: <evidence cited> — when non-obvious]
[**Boundary**: <in/out> — when scope unclear]
[**Risk**: <failure mode> — when non-obvious]

**Assumptions** (any hidden one can matter):
- Confirmed: <user said this>
- Assumed: <defaults I picked, what user will see>
- Open: <don't know yet> → blocks: <what>
- Unexamined: <dimensions not considered yet>

**Questions**:
<Ask about assumptions that could change the direction>
```

**[...] means optional** - include only when it adds value.

Stop and wait for user input. Let the user decide whether to dig deeper. Don't prescribe what you'll do next.

Don't write a file unless explicitly requested. This is a discussion.

## Multi-turn: every response runs the same loop

The skill text is read once and fades. What keeps the loop alive is **what you wrote last round**.

Every response, round 1 or round 7:
- Starts with what you understood (round 1) or what changed (round 2+)
- Cites evidence you read this round
- Shows all assumptions for the current recommendation
- Asks about assumptions that could change direction
- Ends with a stop

**Round 2+ additions**:
- Move answered items: Assumed → Confirmed when user agreed
- **Check what depended on changed items**: when an answer changes an assumption, see what depended on it. Can you drop complexity you added earlier?
- Surface new assumptions the user's answer revealed

## Principles

- Group related decisions. Separate independent ones.
- Can we drop this complexity? What breaks?
- Check evidence yourself. Don't anchor on the user's proposed mechanism.
- Simplest that works.
- Use official solutions before custom design.
- For hard problems: study proven implementations, name the mechanism you're adopting.

## Gates

- Contradiction with project rules or prior decisions: resolve before proceeding
- External dependencies or irreversible effects: make them explicit
- Blocking ambiguity: stays Open (don't fake closure)
- No unlabeled assumption reaches a recommendation
- Any hidden assumption can affect the result—surface all of them

## Boundary with /grill

**talk**: find all assumptions **this recommendation** depends on
- "What needs to be true for this to work?"
- Output: recommendation + complete assumption list

**grill**: explore **the entire decision space** systematically
- "What angles haven't I considered?"
- Output: comprehensive question tree across all dimensions

User wants "did I miss anything?" → `/grill`  
User wants "is this approach sound?" → `talk`

## File output (only on request)

Write a file only when user explicitly asks: "输出到文件" / "write decision record" / "写 ADR" / gives a path.

Output is an ADR. Read `references/ADR-FORMAT.md` and follow it.

Key points:
- Location: `docs/adr/NNNN-slug.md` (increment from highest existing number)
- If `CONTEXT-MAP.md` exists: context-specific decisions go in that context's `docs/adr/`
- Use project's terms from `CONTEXT.md` if it exists
- If this replaces existing ADR: mark old one `superseded by ADR-NNNN`
- If this settles a `proposed` ADR's assumptions: update that to `accepted` instead of duplicating

**Ledger → ADR mapping**:
- Only Confirmed content goes in the decision
- Assumed or Open items the decision depends on → Consequences section ("Assumes X; if wrong, Y changes"), set `status: proposed`
- Low-stakes defaults don't go in ADR
- Constraints not visible in code (legal, compliance, contracts) → one sentence of context

Check before finishing: nothing Assumed reads as settled, the "why" cites the real reason from conversation.
