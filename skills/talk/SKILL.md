---
name: talk
description: >
  Turn a rough idea into an evidence-grounded decision through dialogue. Surfaces hidden assumptions, confirms understanding, recommends one clear direction. Use for planning, architecture choices, scope decisions.
---

# Talk: Evidence-Grounded Decision Through Dialogue

The job is to turn a rough idea into a clear decision through conversation—like colleagues at a whiteboard, not filling out a form.

**Core loop**: listen → understand the real problem → read evidence → give one recommendation → show your assumptions → ask the key questions → wait for the user.

## Why assumptions matter

Rot starts when you meet an unknown, pick the most reasonable default to keep moving, and that default silently becomes fact. It gets coded across modules, tests prove consistency (never correctness), other features build on it. When reality disagrees, the only affordable fix is a patch. Patches pile up until nobody understands the system.

Every step of that chain is cheap to stop at step one, while the assumption is still one sentence. That's what `talk` does: **put assumptions on the table before they become code**.

## The loop (every round, every time)

Every round runs this same loop. By round 5, the conversation history holds four worked examples in your own words—following it the fifth time is natural.

### Round 1: Understand, ground, recommend

1. **Restate what you heard**: "You want to [X] because [Y]. The core question is [Z]."  
   Let the user say "yes" or "no, actually I mean [W]" before you proceed.

2. **Read the evidence**: governing docs, prior decisions (check `docs/adr/`), current code behavior. Cite `path:line` or ADR number. Separate fact from inference.

3. **Find the load-bearing decision**: What one choice determines the others?  
   If multiple independent decisions: stop, suggest `/wayfinder`.  
   If one decision unlocks the rest: work that one.

4. **Pick one direction**: Simplest approach that solves the confirmed need. Evidence-based, not guessed. Mention an alternative only if the tradeoff is genuinely close.

5. **List your assumptions**:
   - **Confirmed**: what the user said or approved
   - **Assumed**: defaults you picked (write as observable behavior: "After delete, past orders stay visible to admins for 30 days")
   - **Open**: what you don't know yet, and what it blocks

6. **Ask 1-2 load-bearing questions**: The assumptions most likely to change the direction. If wrong, what breaks?

7. **Stop and wait**. Don't prescribe next steps. The user decides whether to answer, dig deeper with `/grill`, or move forward.

### Round 2+: Absorb answers, update, repeat

1. **Move the ledger**: answered items → Confirmed; unchallenged low-stakes defaults → Confirmed; skipped questions → stay Assumed

2. **Check what changed**: Does the new answer drop complexity you added earlier? Does it reopen a decision you thought was closed? Adjust the recommendation.

3. **Re-read evidence** if the answer touches code or docs you haven't checked yet.

4. **Surface new gaps**: the user's answer often reveals things they assumed you knew ("oh, obviously deleted users can't log in"). Add those as Confirmed and check what they imply.

5. **Update the recommendation** if needed, or confirm it still holds.

6. **Ask the next frontier**: questions whose answers don't depend on questions still open.

7. **Stop and wait**.

### Last round: Converge

When nothing Open blocks the decision and load-bearing Assumed items are confirmed:

1. Show the final ledger. Anything still Assumed is labeled as such, never quietly promoted.

2. **Restate the decision in plain language**: "We're doing X because Y. This assumes Z—if Z is wrong, W changes."

3. Ask: "Does this match what you had in mind?"

4. Wait for confirmation before implementing or writing the ADR.

## Output format

Keep it simple. The structure should feel like talking, not filling a template.

```
**My understanding**: <restate the core problem in your own words>

**Recommendation**: <one direction, simplest that works, with evidence>

**Assumptions** (only Confirmed goes into code):
- Confirmed: <what the user stated>
- Assumed: <defaults I picked, as observable behavior> → if wrong: <impact>
- Open: <undecided> → blocks: <what>

**Key questions**:
Q1. <concrete scenario>? → my default: <pick>
Q2. ...

<If there are low-stakes defaults: list them as one-liners the user can veto>
```

Scale the format to the decision:
- Simple choice: just recommendation + 1-2 assumptions + 1 question
- Complex decision: full structure

**Write like a colleague at a whiteboard**:
- Short sentences
- Common words over jargon
- If a technical term is needed, say what it means the first time
- Active voice: "I read X and found Y", not "X was examined"
- One idea per sentence

## Multi-turn discipline

The skill text is read once. What keeps the loop alive is **what you wrote last round**. So every response, round 1 or round 7:

- Starts with your understanding of the problem
- Cites evidence you read this round (even if it's "re-read X, no change")
- Shows the ledger
- Ends with questions and a stop

If you skip a step, the user sees it. If you forget to surface assumptions, the ledger stays empty and the pattern breaks.

## Methods (inline how-to)

These come from the `method` skill; you don't need to load it.

**First Principles**: What problem is the user actually solving? Is it real? Don't anchor on the mechanism they proposed.

**Critical Thinking**: Read before claiming. Distinguish fact (cited) from inference. In greenfield, say what doesn't exist yet and mark reasoning as `[inferred from analogy]`.

**High Cohesion, Low Coupling**: Group related decisions, separate independent ones. Independence test: can decisions be made separately without contradicting each other?

**List Uncertainties**: Walk the four gaps.
- Known knowns (user said it) → Confirmed
- Known unknowns (user knows they haven't decided) → Open
- Unknown knowns (obvious to the user, you have to guess) → pick a default, show as observable behavior so they can say yes/no fast
- Unknown unknowns (angles the user never considered) → check a few: other stakeholders, lifecycle, failure modes, scale, what excellence looks like. Only list ones that plausibly change the decision.

**Independent Thinking**: Form your own view before accepting the user's framing. Don't let their proposed solution lock you into solving the wrong problem.

**Occam's Razor**: Simplest approach that solves the confirmed need.

**Ablation**: When an answer changes an assumption, ask what depended on it. Can you drop complexity you added earlier?

## Asking multiple questions without slowing down

Real decisions often have many unknowns. Asking one per round is too slow; dumping them all buries the important ones.

**Batch by dependency**: ask every question whose answer doesn't depend on another open question. That's the frontier. A question that only makes sense after Q2 is answered waits for the next round.

**Every question has a default**: the user can reply "Q1 no, Q3 yes, rest ok" in one line. "Rest ok" confirms your defaults.

**Split by stakes**:
- High stakes (hard to reverse, changes direction): numbered questions, most load-bearing first
- Low stakes (easy to reverse): listed as defaults the user can veto by scanning

**Silence is not consent on high stakes**: a skipped numbered question stays Assumed and comes back at Converge.

If the frontier is very long, the decision probably isn't cut small enough. Go back to "find the load-bearing decision" and cut it smaller.

## Things to remember

- Finding facts is your job. Read the repo; don't ask the user what you can look up.
- Don't fake closure. If something is Open and stays open, say that.
- Contradiction with project rules or prior decisions: surface it and resolve it before recommending.
- Don't route to other skills unprompted. If the user wants deeper exploration, they'll ask or use `/grill`.
- Keep the ledger short. If it grows long, the decision isn't cut small enough.

## File output (only on request)

Write a file only when the user explicitly asks: "输出到文件", "写 ADR", "write the decision record", or gives a path.

Output is an ADR. Read `references/ADR-FORMAT.md` and follow it. Key points:

- Location: `docs/adr/NNNN-slug.md` (increment from highest existing number)
- If `CONTEXT-MAP.md` exists: context-specific decisions go in that context's `docs/adr/`
- Use the project's terms from `CONTEXT.md` if it exists
- If this replaces an existing ADR: mark the old one `superseded by ADR-NNNN`
- If this settles a `proposed` ADR's assumptions: update that ADR to `accepted` instead of writing a duplicate

**Ledger → ADR mapping**:
- Only Confirmed content is written as the decision
- Assumed or Open items the decision depends on → Consequences section, one line each ("Assumes X; if wrong, Y changes"), and set `status: proposed`
- Low-stakes defaults don't go in the ADR
- Constraints not visible in code (legal, compliance, partner contracts) → one sentence of context

Check before finishing: nothing Assumed reads as settled, the "why" cites the real reason from the conversation.
