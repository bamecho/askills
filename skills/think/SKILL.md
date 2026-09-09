---
name: think
description: >
  Turn a rough idea into an evidence-grounded decision. Use for planning direction, scope judgment, architecture choices, feasibility. Output assumptions explicitly, expect iterative discussion with user.
---

# Think: Evidence-Grounded Decision

Turn rough idea → grounded recommendation + visible assumptions. **Expect discussion**, not one-shot answer.

## Method

**First principles thinking** → cut to load-bearing decision → ground in evidence → surface assumptions → ask about key assumption → pause for discussion.

### 1. Cut to Core

Apply **first principles**: what's the actual problem? One load-bearing decision usually determines others.

**Independence test**: can decisions be made separately without creating contradictions?
- If yes (multiple independent decisions): stop, suggest `wayfinder`
- If no (one determines others): proceed with load-bearing decision

### 2. Ground in Evidence

Read: governing docs, prior decisions, current behavior, relevant code.

**Critical thinking**: distinguish fact from inference. Cite `path:line` or source.

**When evidence missing** (greenfield/new domain):
- State explicitly what doesn't exist yet
- Ground in: user constraints, external contracts, proven patterns for similar problems
- Mark all reasoning as `[inferred from analogy]`

### 3. Recommend One Direction

**Occam's razor**: simplest approach that solves, grounded in evidence.

Structure (compact):
- **Decision**: <one line what + why>
- **Rationale**: decisive evidence (cited)
- **Boundary**: what's in/out
- **Risk**: most likely failure mode

Mention alternative only if tradeoff genuinely close.

### 4. Surface Assumptions

**Mark explicitly**, never bury:
- **Confirmed**: user stated outcome/constraints (fixed)
- **Assumed**: choice made to proceed, user never confirmed (mark as `[assumed]`)
- **Open**: answer would change direction, still unknown (+ impact + owner)
- **Unexamined**: dimension user hasn't raised (name class, don't answer)

**Assumption hygiene**: assumed/unexamined is where decisions rot. Implementation proves consistency, never correctness. Surface while still one sentence to change.

**Classification boundary**:
- Confirmed vs Assumed: if user said "I want it fast" → speed is confirmed requirement; what "fast" means numerically is assumed
- Assumed vs Open: assumed has plausible default you picked; open has no defensible default yet
- Open vs Unexamined: open is specific (performance target); unexamined is class (have you considered operational costs?)

### 5. Ask & Pause

Ask about the **single most load-bearing assumption** (the one most likely to change the direction).

Output recommendation with assumptions stated.

**Stop and wait for discussion**. User may:
- Answer the key assumption → refine recommendation
- Use `batch-grill-me` or similar to probe further
- Request file output after discussion converges

Output format (chat only at this stage):
- Simple decision: 2-4 lines
  ```
  **Decision**: <what + why>
  **Key assumption**: <question about load-bearing assumption>
  **Other assumptions**: <list with [assumed] markers>
  ```
- Complex decision: structured bullets
  ```
  **Decision**: <what + why>
  **Rationale**: <evidence cited>
  **Boundary**: <in/out>
  **Risk**: <failure mode>
  
  **Key assumption**: <question>
  **Assumptions**: <list with [assumed] markers>
  **Open**: <list with impact + owner>
  **Unexamined**: <classes of questions>
  ```

Do not write file at this stage. Wait for user feedback.

## Discipline

- **High cohesion, low coupling**: group related decisions, separate independents
- **Ablation thinking**: can we drop this complexity? What breaks?
- **Independent thinking**: check evidence yourself, don't anchor on user's proposed mechanism
- **Occam's razor**: simplest that works (applied in step 3)
- Official solutions before custom design
- For hard problems: study proven implementations, name adopted mechanism

## Gates

- Contradiction with project rules/approved decisions: resolve before proceeding
- External dependencies/irreversible effects: explicit
- Blocking ambiguity: stays Open (don't fake closure)
- No unlabelled assumption reaches recommendation

## Output

Return recommendation itself. Omit workflow narration.

**Decision record** (only when user explicitly asks: "输出到文件" / "write decision record" / provides path):

Write to `docs/decisions/NN-<slug>.md`. `NN` = next unused number.

Structure:
1. **Decision**: what + why (2-3 lines)
2. **Observable outcome**: how to verify it worked
3. **Rationale**: what decided it (cited evidence)
4. **Boundary**: scope (what's in/out)
5. **Assumptions**: choices made to proceed (each marked `[assumed]`)
6. **Open questions**: still unknown (each with: impact + default if exists + owner)
7. **Unexamined**: dimension classes user hasn't raised
8. **Risk**: most likely failure mode
9. **Alternatives considered**: rejected options + why (if relevant)

No invented details. Point at facts, don't restate.

**Verify before completion**:
□ Every assumption explicitly marked `[assumed]`
□ Every open question has impact + owner
□ Rationale cites real evidence (`path:line` or source)
□ No fake closure (blocking ambiguity stays Open)
□ Observable outcome is measurable (not just "success")

**Note**: Most users proceed directly to implementation after discussion. Only write file when explicitly requested.