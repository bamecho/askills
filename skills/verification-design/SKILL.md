---
name: verification-design
description: "Design concrete verification for work slices. Define what observation proves correctness and rejects plausible wrong outputs. Use when planning agent work or strengthening weak acceptance criteria."
---

# Verification Design

Design verification that proves work is correct.

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
- Work combines independent outcomes (example: "add login + add dashboard" → two slices)
- Contains unresolved design decision (can't verify until decision made)
- Crosses competing module ownership (different owners can't agree on intermediate state)
- Cannot be verified until later work exists (half-done feature has no observable behavior)

**Keep together when**:
- Separation creates meaningless intermediate state (example: "add database table" without "add query to use it" is unverifiable)
- Changes are coupled (one doesn't work without the other)

**Key point**: smallest independently provable outcome, not smallest edit. A one-line change that requires reading 10 files to understand is NOT bounded. A 50-line change with clear input/output IS bounded.

### 2. Choose verification that rejects wrong results

**Critical thinking required**: how could this be wrong while looking finished?

Think through the failure modes:
- Could return cached/hardcoded data instead of computing it?
- Could skip validation and accept invalid input?
- Could write to wrong location (test DB instead of real DB)?
- Could use test double that always succeeds?
- Could look right in UI but not persist?

**Strong verification**: 
- Observes the actual outcome
- Would fail for material wrong results listed above
- Exercises real code path user takes

**Weak verification**: 
- Passing tests that don't check the actual outcome
- High coverage number (coverage shows what ran, not what's correct)
- Agent report "looks good to me" (agent can't distinguish right from plausible wrong)

**Pick from narrowest to broadest** (stop when you've rejected the failure modes):

1. **Type/lint/schema**: structural properties they actually check
   - Use when: interface contracts, data structure shapes, syntax rules
   - Limitation: doesn't prove runtime behavior or business logic

2. **Unit/property tests**: deterministic rules, boundaries, input classes
   - Use when: pure logic, edge cases, validation rules
   - Limitation: mocked dependencies hide integration problems

3. **Integration tests**: cross-module behavior, persistence, adapters
   - Use when: need to prove modules work together, data persists, adapters translate correctly
   - Limitation: slower, needs real dependencies or realistic fakes

4. **End-to-end**: user-visible behavior, real runtime wiring
   - Use when: need to prove user flow works, all layers integrated
   - Limitation: slowest, can be flaky, hard to isolate failures

5. **Manual check**: visual output, UX, accessibility
   - Use when: automation can't observe the right thing (visual design, screen reader behavior)
   - Limitation: not repeatable, human judgment required

**Critical**: use repository-native checks. Don't invent plausible commands. If you don't know the actual test command, mark it as unknown.

### 3. Make it concrete

**Format**: `[Action] → [Observable result] — [What wrong implementation this rejects]`

**Examples of strong verification**:

- "Run `npm test auth.test.ts` → all 5 JWT tests pass, including 'rejects expired token' → rejects implementation that doesn't check expiry"

- "Start app, POST /api/users with duplicate email → returns 409 Conflict → rejects implementation that doesn't enforce unique constraint"

- "Navigate to /settings, screenshot shows timezone picker with 'America/Los_Angeles' selected → rejects implementation that renders UI but doesn't load user preference"

- "`git log --oneline` shows squashed commit with attribution → rejects implementation that force-pushed without preserving attribution"

**Examples of weak verification (DO NOT USE)**:

- "Write tests" — what tests? what do they check? could be empty test that passes
- "Verify it works" — how? what's the evidence? agent can't verify this
- "Run all tests" — which ones matter? if 100 tests and 1 new test added, which one proves this change?
- "High test coverage" — coverage shows code ran, not that assertions are correct
- "Code looks correct" — agent judgment is not evidence

**Key point**: if you can't name the specific command/action and expected result, the verification is too weak. Strengthen it before proceeding.

### 4. Check the proof

Before accepting the verification, challenge it with these questions:

**Could stubbed/incomplete work pass?**
- Could I return hardcoded data and pass? → verification needs to check computed result
- Could I skip the database write and pass? → verification needs to query DB after
- Could I use a test double that always succeeds? → verification needs real dependency or realistic fake

**Does it exercise the real path?**
- Does the check use the same entry point the user uses?
- Or does it call a test-only function that bypasses validation?
- Does it verify through the UI/API/CLI or through internal methods?

**Does it verify side effects?**
- If code writes a file, does verification check the file exists and has correct content?
- If code inserts DB row, does verification query to confirm?
- If code sends email, does verification check outbox or mock was called with right args?

**Does it capture both action and result?**
- Not just "screenshot shows success message" but "after POST returns 201, screenshot shows success AND GET /api/users includes the new user"

**Could mocked dependencies hide problems?**
- If external API is mocked, does mock behavior match real API?
- If database is faked, does fake enforce same constraints as real DB?

**Add negative/boundary/regression checks only when failure model gives them value**:
- Negative case: if validation is the risk, add "rejects invalid input" check
- Boundary case: if edge case is the risk, add "handles empty list" check
- Regression case: if specific bug returned, add check that prevents it

Don't add checks because they "should" exist. Add them because they reject a plausible failure.

## Verification methods reference

| Method | Good for | Watch out |
|--------|----------|-----------|
| Type check | Structure, contracts, interface shape | Doesn't prove runtime behavior or business logic |
| Unit test | Pure logic, boundaries, edge cases, validation rules | Mocked dependencies hide integration problems |
| Integration test | Cross-module behavior, persistence, adapters, data flow | Slower, needs real dependencies or realistic fakes |
| End-to-end | User flows, real wiring, all layers together | Slowest, can be flaky, hard to isolate failures |
| Manual check | Visual output, UX details, accessibility with screen readers | Not repeatable, requires human judgment |
| Performance test | Speed, resource usage, scalability | Needs baseline, threshold, realistic workload |

**Not a checklist**. Pick what rejects wrong results for this specific change. One strong check beats five weak checks.

## Output

For each slice, state:
- **What**: one sentence describing the change
- **Verification**: concrete observation in format `[Action] → [Result] — [What it rejects]`
- **Why this check**: what plausible wrong implementation wouldn't pass

No prescribed format. Make it clear what decides success.

## If verification is weak

**Symptoms**:
- "Verify the feature works" — how? what's the evidence?
- "Run all tests" — which ones matter? what do they check?
- "Agent confirms it's correct" — agent can't tell wrong from right without observable evidence
- "High test coverage" — coverage shows what ran, not what's correct
- Stubbed/hardcoded work could pass

**Fix**:
1. Name the specific test/check/observation
2. Say what observable result proves correctness
3. Show it would fail for a plausible wrong implementation
4. If you can't name the command, mark it as unknown rather than guessing

**Example transformation**:

Weak: "Add user validation and write tests"

Strong: "Add user validation → `npm test user.test.ts` passes all 3 cases: accepts valid email, rejects malformed email (returns 400), rejects duplicate email (returns 409) — rejects implementation that doesn't check format or uniqueness"
