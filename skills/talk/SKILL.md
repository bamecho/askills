---
name: talk
description: "Turn a rough idea into an evidence-grounded decision through dialogue. Surfaces all hidden assumptions before they become code. Use for planning, architecture choices, scope decisions."
---

# Talk: Find the Real Problem, Surface All Assumptions

Turn rough idea → clear decision + visible assumptions.

Apply this method every round. Re-ground in evidence. Re-check assumptions. Keep finding the real problem.

## Why this matters

Problems start when you meet an unknown. You pick a reasonable default. That default becomes fact. It gets coded, tested, and built upon. When reality disagrees, patches pile up.

Stop this at step one, while the assumption is still one sentence. **Any hidden assumption can affect the result.**

## Language

Use ASD-STE100 (Simplified Technical English): approved words, one meaning per word, ≤20 words per sentence, active voice, observable actions.

**Test**: if the user would ask "what does that mean?", rewrite it.

## Method

### 1. Say what you heard

"You want [X] because [Y]. The real question is [Z]."

Wait for the user to say "yes" or "no, I mean [W]".

### 2. Find the real problem

What is the user actually trying to solve? Look past the mechanism they proposed.

**Independence test**: can these decisions be made separately without contradicting each other?
- Yes → stop, these are separate decisions to make independently
- No → find the one decision that determines the others

### 3. Read the evidence

Read: governing docs, `docs/adr/`, current code, behavior.

Separate fact from inference. Cite `path:line` or source.

**When evidence does not exist**: say what is missing, ground in user constraints and proven patterns, mark reasoning `[inferred from analogy]`.

### 4. Recommend one direction

Simplest approach that solves the problem, grounded in evidence.

Include:
- What and why (one line)
- Evidence that led here (when non-obvious)
- What is in scope, what is out (when unclear)
- Most likely failure mode (when non-obvious)

Mention alternative only if tradeoff is genuinely close.

### 5. Surface every assumption

List every assumption this recommendation depends on.

- **Confirmed**: user said this
- **Assumed**: default you picked — write what user will see: "After delete, past orders visible to admins for 30 days"
- **Open**: do not know yet, blocks something — say what
- **Unexamined**: dimension user has not raised — name the class

**Classification**:
- Confirmed vs Assumed: user said "fast" → speed is confirmed; what "fast" means as number is assumed
- Assumed vs Open: assumed has plausible default; open has no defensible default
- Open vs Unexamined: open is specific; unexamined is a class

### 6. Ask about the assumptions

Ask about assumptions that could change the direction. Not just one—**any hidden assumption can matter**.

**Stop and wait.** User may answer, use `/grill` to explore systematically, or request a file.

## What to include in your response

Every response should cover:

1. **Your understanding**: what you heard, what the real problem is
2. **Your recommendation**: one direction, simplest that works, grounded in evidence
3. **All assumptions**: confirmed/assumed/open/unexamined — any hidden one can affect the result
4. **Key questions**: about assumptions that could change the direction

**How to say it**: naturally, like talking to a colleague. Not filling a template.

Do not write file unless explicitly requested. This is a discussion.

## Multi-turn

Every response:
- Start with what you understood (round 1) or what changed (round 2+)
- Cite evidence you read this round
- Show all assumptions for current recommendation
- Ask about assumptions that could change direction
- Stop

**Round 2+**: move answered items in ledger. Check what depended on changed items—can you drop complexity you added earlier?

## Discipline

- Group related decisions, separate independent ones
- Can we drop this complexity? What breaks?
- Check evidence yourself, do not anchor on user's proposed mechanism
- Simplest that works
- Official solutions before custom design

## Gates

- Contradiction with project rules/prior decisions: resolve first
- External dependencies/irreversible effects: explicit
- Blocking ambiguity: stays Open (do not fake closure)
- No unlabeled assumption reaches recommendation
- Any hidden assumption can affect result—surface all

## Boundary with /grill

**talk**: find all assumptions **this recommendation** depends on ("what needs to be true for this to work?")

**grill**: explore **entire decision space** systematically ("what angles have not I considered?")

## File output (on request only)

Write only when user asks: "输出到文件" / "write decision record" / "写 ADR" / gives path.

Output is ADR. Read `references/ADR-FORMAT.md` and follow it.

**Structure for ADR file**:
- Location: `docs/adr/NNNN-slug.md` (increment from highest)
- Use project terms from `CONTEXT.md` if exists
- If replaces existing ADR: mark old `superseded by ADR-NNNN`
- If settles `proposed` ADR assumptions: update to `accepted` instead of duplicating

**Ledger → ADR mapping**:
- Only Confirmed → decision
- Assumed/Open items decision depends on → Consequences ("Assumes X; if wrong, Y changes"), set `status: proposed`
- Low-stakes defaults: omit
- Constraints not in code (legal, compliance): one sentence context

Check: nothing Assumed reads as settled, "why" cites real reason from conversation.
