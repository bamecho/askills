---
name: design-verifiable-slices
description: "Turn work into bounded slices with concrete verification. Each slice has clear success criteria that reject plausible wrong outputs. Use when planning agent work or choosing verification methods."
---

# Design Verifiable Slices

Turn work into slices an agent can complete and prove correct.

## When to use

Use when:
- Planning work for an agent
- Success criteria are weak or missing
- Need to choose verification method
- Task risks losing context across rounds

## Core principle

**Verification is the boundary, not ceremony after**. Shape work so the result can be distinguished from credible wrong results by direct evidence.

## Method

### 1. Bound the work

One coherent change an agent can understand and verify before moving to unrelated work.

**Split when**:
- Work combines independent outcomes
- Contains unresolved design decision
- Crosses competing module ownership
- Cannot be verified until later work exists

**Keep together when**:
- Separation creates meaningless intermediate state
- Changes are coupled (one doesn't work without the other)

Smallest independently provable outcome, not smallest edit.

### 2. Choose verification that rejects wrong results

Think: how could this be wrong while looking finished?

**Strong verification**: observes the actual outcome, would fail for material wrong results.

**Weak verification**: passing tests, high coverage, or agent report that doesn't distinguish right from plausible wrong.

**Pick from narrowest to broadest**:
- **Type/lint/schema**: structural properties they actually check
- **Unit/property tests**: deterministic rules, boundaries, input classes
- **Integration tests**: cross-module behavior, persistence, adapters
- **End-to-end**: user-visible behavior, real runtime wiring
- **Manual check**: when automation can't observe the right thing

Use repository-native checks. Don't invent plausible commands.

### 3. Make it concrete

Say what observation proves success and why plausible wrong implementations wouldn't pass.

**Examples**:
- "Run `npm test auth.test.ts` - checks JWT validation rejects expired tokens"
- "Start app, navigate to /settings, screenshot shows new timezone picker"
- "POST /api/users returns 201, GET /api/users includes new user, no duplicate email allowed"

**Not this**:
- "Write tests" (what tests? what do they check?)
- "Verify it works" (how? what's the evidence?)
- "High test coverage" (coverage doesn't prove assertions)

### 4. Check the proof

Before accepting the slice, ask:

Could unchanged/stubbed/hardcoded/partially wired work pass this verification?

If yes, strengthen it. Show a focused check that detects the absent or wrong behavior.

**Challenge questions**:
- Does the check exercise the real user path or a test-only shortcut?
- Does it verify side effects (files written, DB rows, messages sent)?
- Does it capture both action and resulting state?
- Could mocked dependencies hide broken integration?

Add negative/boundary/regression checks only when the actual failure model gives them value.

## Verification methods

| Method | Good for | Watch out |
|--------|----------|-----------|
| Type check | Structure, contracts | Doesn't prove runtime behavior |
| Unit test | Logic, boundaries, edge cases | May not catch integration issues |
| Integration test | Cross-module behavior, persistence | Slower, needs real dependencies |
| End-to-end | User flows, real wiring | Slowest, can be flaky |
| Manual check | Visual output, UX, accessibility | Not repeatable |
| Performance test | Speed, resource usage | Needs baseline and threshold |

Not a checklist. Pick what rejects wrong results for this change.

## Output

For each slice, state:
- **What**: one sentence describing the change
- **Verification**: concrete observation that proves it works (command, action, expected result)
- **Why this check**: what plausible wrong result it rejects

No prescribed format. Make it clear what decides success.

## If verification is weak

**Symptoms**:
- "Verify the feature works" (how?)
- "Run all tests" (which ones matter? what do they check?)
- "Agent confirms it's correct" (agent can't tell wrong from right)
- Stubbed work could pass

**Fix**:
- Name specific test/check/observation
- Say what it proves
- Show it fails for wrong implementation
