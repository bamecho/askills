---
name: verification-design
description: "Design concrete verification for work slices. Define what observation proves correctness and rejects plausible wrong outputs. Use when planning agent work or strengthening weak acceptance criteria."
---

# Verification Design

Design verification that proves work is correct.

## When to use

Use when planning agent work, when success criteria are weak or missing, when need to choose verification method, or when task risks losing context across rounds.

## Core principle

Verification is the boundary, not ceremony after. Shape work so the result can be distinguished from credible wrong results by direct evidence.

## Method

### 1. Bound the work

One coherent change an agent can understand and verify before moving to unrelated work.

Split when: work combines independent outcomes, contains unresolved design decision, crosses competing ownership, cannot verify until later work exists.

Keep together when: separation creates meaningless intermediate state, changes are coupled.

Smallest independently provable outcome, not smallest edit.

### 2. Choose verification that rejects wrong results

Think: how could this be wrong while looking finished?

Common failure modes: returns cached/hardcoded data, skips validation, writes to test location, uses mock that always succeeds, UI renders but data doesn't persist.

**Strong verification**: observes actual outcome, would fail for material wrong results.

**Weak verification**: passing tests that don't check outcome, high coverage number, agent report without observable evidence.

Pick from narrowest to broadest: type/lint/schema for structure, unit/property for logic and boundaries, integration for cross-module behavior, end-to-end for user flows, manual for visual/UX/accessibility.

Use repository-native checks. Don't invent commands.

### 3. Make it concrete

Required format: `[Action] → [Observable result] — [What wrong implementation this rejects]`

Example of strong: "Run `npm test auth.test.ts` → JWT test 'rejects expired token' passes — rejects implementation that doesn't check expiry"

Example of weak: "Write tests" (no specific command), "Verify it works" (no observable evidence), "High test coverage" (shows what ran, not what's correct)

If you can't name the specific command/action and expected result, the verification is too weak. Strengthen it or mark unknown.

### 4. Check the proof

Challenge before accepting:

Could stubbed/incomplete work pass? → need to check computed result, query DB after write, use real dependency or realistic fake

Does it exercise real path? → same entry point user uses, not test-only function

Does it verify side effects? → file written (check exists with correct content), DB row inserted (query confirms), email sent (check outbox or mock args)

Does it capture action AND result? → not just success message, but action taken and state changed

Add negative/boundary/regression checks only when failure model gives them value.

## Verification methods

| Method | Good for | Limitation |
|--------|----------|-----------|
| Type/lint | Structure, contracts | Doesn't prove runtime behavior |
| Unit test | Logic, boundaries | Mocked dependencies hide integration issues |
| Integration | Cross-module, persistence | Slower, needs real dependencies |
| End-to-end | User flows, real wiring | Slowest, can be flaky |
| Manual | Visual, UX, accessibility | Not repeatable |

Pick what rejects wrong results for this change.

## Output

For each slice state: what (one sentence), verification (`[Action] → [Result] — [What it rejects]`), why this check (what wrong implementation wouldn't pass).

No prescribed format. Make clear what decides success.

## If verification is weak

Symptoms: "verify it works", "run all tests", "agent confirms", stubbed work could pass.

Fix: name specific command, say observable result, show it fails for wrong implementation.
