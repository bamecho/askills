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

**Split when**: work combines independent outcomes, contains unresolved design decision, crosses competing ownership, cannot verify until later work exists.

**Keep together when**: separation creates meaningless intermediate state, changes are coupled.

Smallest independently provable outcome, not smallest edit.

### 2. Choose verification that rejects wrong results

**Think: how could this be wrong while looking finished?**

Common failure modes that look right but aren't:
- Returns cached/hardcoded data instead of computing
- Skips validation, accepts invalid input
- Writes to test location instead of real location
- Uses mock that always succeeds
- UI renders but data doesn't persist

**Strong verification**: observes actual outcome, would fail for these wrong results.

**Weak verification**: passing tests that don't check outcome, high coverage number, agent says "looks good".

**Pick from narrowest to broadest**:
- Type/lint/schema: structure, contracts
- Unit/property: logic, boundaries, edge cases
- Integration: cross-module, persistence, adapters
- End-to-end: user flows, real wiring
- Manual: visual, UX, accessibility

Use repository-native checks. Don't invent commands.

### 3. Make it concrete

**Required format**: `[Action] → [Observable result] — [What wrong implementation this rejects]`

**Strong examples**:
- "Run `npm test auth.test.ts` → JWT test 'rejects expired token' passes — rejects implementation that doesn't check expiry"
- "POST /api/users with duplicate email → returns 409 — rejects implementation without unique constraint"

**Weak examples (never use)**:
- "Write tests" — no specific command or check
- "Verify it works" — no observable evidence
- "High test coverage" — coverage shows what ran, not what's correct

**Critical**: if you can't name the specific command/action and expected result, the verification is too weak. Strengthen it or mark unknown.

### 4. Check the proof

Before accepting, challenge with these questions:

**Could stubbed/incomplete work pass?**
- Return hardcoded data and pass? → check computed result
- Skip database write and pass? → query DB after
- Use mock that always succeeds? → need real dependency or realistic fake

**Does it exercise real path?**
- Same entry point user uses, not test-only function

**Does it verify side effects?**
- File written → check file exists with correct content
- DB row inserted → query confirms
- Email sent → check outbox or mock args

**Does it capture action AND result?**
- Not just "success message" but "POST returns 201 AND GET includes new item"

Add negative/boundary/regression checks only when failure model gives them value. Don't add because they "should" exist.

## Verification methods

| Method | Good for | Limitation |
|--------|----------|-----------|
| Type/lint | Structure, contracts | Doesn't prove runtime behavior |
| Unit test | Logic, boundaries | Mocked dependencies hide integration issues |
| Integration | Cross-module, persistence | Slower, needs real dependencies |
| End-to-end | User flows, real wiring | Slowest, can be flaky |
| Manual | Visual, UX, accessibility | Not repeatable |

Pick what rejects wrong results for this change. One strong check beats five weak checks.

## Output

For each slice:
- **What**: one sentence describing change
- **Verification**: `[Action] → [Result] — [What it rejects]`
- **Why this check**: what wrong implementation wouldn't pass

No prescribed format. Clear what decides success.

## If verification is weak

**Symptoms**: "verify it works", "run all tests", "agent confirms", stubbed work could pass.

**Fix**: name specific command, say observable result, show it fails for wrong implementation.

**Example**:

Weak: "Add user validation and write tests"

Strong: "Add user validation → `npm test user.test.ts` passes 3 cases: accepts valid email, rejects malformed (400), rejects duplicate (409) — rejects implementation without format or uniqueness checks"
