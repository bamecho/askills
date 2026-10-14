---
name: talk
description: "Turn a rough idea into an evidence-grounded decision through dialogue. Surfaces all hidden assumptions before they become code. Use for planning, architecture choices, scope decisions."
---

# Talk

Turn rough idea into clear decision.

## Why this matters

You pick a default. That default becomes fact. It gets coded and built upon. When reality disagrees, patches pile up.

Stop this while the assumption is still one sentence.

## Language

Use ASD-STE100: simple words, ≤20 words per sentence, one meaning per sentence.

**No abstract terms**: Say the actual thing. Not "approach" or "mechanism", but "cache the result" or "check the flag first".

**No vague phrases**: Say what changes. Not "optimize performance", but "cut query time from 800ms to 50ms".

**Use consistent terms**: Pick one word for each thing and keep using it.

**Be concrete**: Say what actually happens.

## How to respond

### First round

1. **Repeat what the user wants**: Use your own words to restate what you think the user's goal is, and what problem they are trying to solve.

2. **Find the real problem**: Use `/find-core-problem` skill to analyze what the user is actually trying to solve.

3. **Read the evidence**: Use explorer agent to read governing docs, `docs/adr/`, current code. Cite `path:line`. Separate fact from inference.

4. **Recommend one direction**: Simplest approach that solves the problem. Say what and why in one line. Add evidence if non-obvious.

5. **List what you assumed**: Say the actual default. Not "reasonable TTL", but "cache expires after 5 minutes".

6. **Ask what blocks progress**: What needs to be confirmed before you can proceed?

### Later rounds

Start with what changed. Update your recommendation if needed. Ask remaining questions.

## Rules

- Read evidence yourself, don't trust user's proposed mechanism
- Simplest that works
- If contradicts project rules: resolve first
- If cannot be reversed: say so explicitly
- If you don't know: leave it open, don't guess

## Boundary with /grill

**talk**: find assumptions this recommendation depends on

**grill**: explore entire decision space systematically
