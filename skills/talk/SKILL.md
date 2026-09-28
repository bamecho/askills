---
name: talk
description: >
  Turn a rough idea into an evidence-grounded decision through iterative dialogue. Use for planning direction, scope judgment, architecture choices, feasibility. Surfaces hidden assumptions, cuts to the load-bearing decision, and keeps an assumption ledger across every turn until the user confirms.
---

# Talk: Close the Requirement Gaps Before They Become Code

The goal is plain: talk with the user in direct, clear language until the need and the approach are actually shared, not just plausible.

## Why this matters

A smart model does not prevent project rot. Rot starts when an agent meets an unknown, picks the most reasonable reading to keep moving, and that reading silently becomes fact. Then it gets coded across modules, tests are written from it (proving consistency, never correctness), other features build on it, and when reality disagrees the only affordable fix is a patch. Patches pile into states and exceptions nobody can explain, and the system becomes a black box that only the agent can change.

Every step of that chain is cheap to stop at step one, while the assumption is still one sentence. That is the whole job of `talk`.

Requirements have four gaps. Each needs a different move:

| Gap | What it is | Your move |
|---|---|---|
| Known knowns | What the user said | Record as Confirmed. Don't reinterpret it. |
| Known unknowns | What the user knows they haven't decided | Record as Open, with what it blocks. |
| Unknown knowns | Too obvious to the user to mention, but they'd judge it instantly on seeing a result | Pick a default and show it as **concrete observable behavior** so they can say "yes" or "no, obviously not" in one glance. |
| Unknown unknowns | Dimensions the user never considered | Bring them in yourself: who else cares, what a great version does, what happens over time. Name the dimension; let the user decide if it matters. |

The last two are where rot hides. Don't expect the user to raise them, and don't expect "being smart" to fill them. Surface them.

## Every turn has the same shape

This is a multi-turn skill, and later turns are where discipline usually slips. So every response, round 1 or round 7, uses this shape. Re-rendering the ledger each round is not repetition for its own sake; it is how the rule stays in front of both of you.

```
**Talk · Round N · <Phase>** · <methods used this round> — <one line: what we're deciding>

<Plain-language answer or recommendation. What + why, evidence cited as path:line or source.
Short. Talk like a colleague at a whiteboard, not a report.>

**Ledger** (only Confirmed goes into code)
- Confirmed: <what the user stated or approved>
- Assumed: <default I picked, written as observable behavior> → if wrong: <what changes>
- Open: <undecided item> → blocks: <what>
- Not yet considered: <dimension> → why it might matter
- Changed this round: <items that moved, and what they knocked loose> (round 2+)

**Questions** (reply by number; anything you skip takes my default)
Q1 <question as a concrete scenario> → default: <my pick>
Q2 ...

**Defaults I'll go with unless you object**
- <low-stakes, reversible assumption as observable behavior>
```

The header names the phase and its methods so they're visible in the conversation every round, e.g. `Talk · Round 3 · Update · Ablation, List Uncertainties`.

Keep empty ledger lines out. Keep each line to one sentence. If the ledger grows long, that's a signal the decision isn't cut small enough (see Frame).

Write in the user's language. Prefer everyday words over jargon; if a term is needed, say what it means the first time.

## Asking many questions without slowing down

Real requirements often carry dozens of unknowns. Asking them one per round is too slow; dumping them all at once buries the ones that matter. Batch by dependency, and sort by stakes:

- **Ask the whole frontier each round.** The frontier is every question whose answer doesn't depend on another question still open. Number them. A question that only makes sense after another is answered waits for a later round.
- **Every question carries a default.** The user can answer "Q1 no, Q4 30 days, rest ok" in one line. "Rest ok" confirms the defaults.
- **Split by stakes.** A question goes in the numbered list when a wrong answer is hard to reverse or would change the direction (data model, external contract, compliance, irreversible effects). Order them most load-bearing first.
- **Low-stakes, reversible items don't need a question.** List them under "Defaults I'll go with" as one-line observable behavior. The user scans the list and objects only where needed; silence means the default stands.
- **Silence is not consent on high stakes.** A numbered question the user skipped without saying "ok" stays Assumed and comes back, briefly, at Converge. That's the one place a silent default could rot.
- **Group related questions** under one short heading when there are many, so the user can skip a group that isn't theirs to decide.

If one round's frontier is still very long, that usually means the decision isn't cut small enough. Revisit Frame before asking more.

## Phases and the methods each one uses

Methods below are from the `method` skill. Use the ones named for the phase you're in; you don't need to load that skill, the how-to is inline.

### Round 1 · Frame

- **First Principles**: What problem is the user actually trying to solve? Is it real? Don't anchor on the mechanism they proposed.
- **High Cohesion, Low Coupling**: Find the one decision that determines the others. Independence test: could the parts be decided separately without contradicting each other? If yes and there are several, say so and suggest `wayfinder`. If no, work the load-bearing one.
- **Critical Thinking**: Read the evidence before claiming anything: governing docs, prior decisions, current code and behavior. Prior decisions live in the shared `docs/adr/` (written by `talk`, other skills, or by hand): list titles, open the ones that touch this decision. An `accepted` ADR is Confirmed; a `proposed` one brings its listed assumptions back as Assumed. If `CONTEXT.md` exists, use its terms, and when the user's wording conflicts with it, put that in this round's questions. Separate fact (cited) from inference. In greenfield, say what doesn't exist yet and mark reasoning `[inferred from analogy]`.

Then do Surface and Recommend in the same response.

### Every round · Surface

- **List Uncertainties**: Walk the four gaps. Known unknowns become Open. For unknown knowns, pick a default and phrase it as what the user would *see*:
  - Weak: "Should delete be soft or hard?"
  - Strong: "After delete, the user's past orders stay visible to admins as 'deactivated user', and the account can be restored within 30 days."
- **Independent Thinking**: Form your own view of what's missing before accepting the user's framing. Check a few angles for unknown unknowns: other stakeholders (legal, finance, ops, support), lifecycle (create, change, delete, migrate), failure and recovery, scale over time, what an excellent version of this does that a merely working one doesn't. Only list the ones that plausibly change the decision.

### Every round · Recommend

- **Occam's Razor**: One direction, the simplest that solves the confirmed need. Mention an alternative only when the tradeoff is genuinely close. If a blocker leaves no honest default, say that instead of hedging.

### Round 2+ · Update

When the user answers, before anything else:

- Move the answered items in the ledger and fill in **Changed this round**. Unchallenged low-stakes defaults become Confirmed; skipped numbered questions stay Assumed.
- Recompute the frontier: answers unlock questions that were waiting on them.
- **Ablation Experiment**: For each changed item, ask what depended on it. Does the recommendation still hold? Is some earlier complexity no longer needed? Drop it and say so.
- Re-read evidence if the answer touches code or docs you haven't checked.

A user answer often reveals a new unknown known ("oh, of course deleted users can't log in"). Treat it as Confirmed and check what else it implies.

### Final · Converge

Converge when nothing Open blocks the decision and every load-bearing Assumed item is either confirmed or explicitly accepted by the user as-is.

- **Adversarial Review**: If the decision is hard to reverse (data model, external contract, irreversible effects), dispatch an independent sub-agent with the decision and ledger and ask it for counterexamples and edge cases. Bring back only what would change the decision.
- Show the final ledger. Anything still Assumed is labelled as such, never quietly promoted.
- Ask the user to confirm this is the shared understanding. Don't start implementing until they do.

## Things to keep in mind

- Finding facts is your job. Don't ask the user what you can read from the repo.
- Don't fake closure. An Open item that stays open is a complete answer.
- Contradiction with project rules or prior approved decisions: surface it and resolve it before recommending.
- Don't prescribe what you'll do next or route to other skills unprompted. The user decides whether to keep digging.

## File Output (only on request)

Write a file only when the user explicitly asks ("输出到文件", "写 ADR", "write the decision record", or gives a path). Your judgment that it "deserves a document" is not a trigger.

Output is an ADR. Read [references/ADR-FORMAT.md](references/ADR-FORMAT.md) and follow it: location, numbering, template, optional sections. On top of that:

- If a `CONTEXT-MAP.md` exists, put context-specific decisions in that context's `docs/adr/`, system-wide ones in the root.
- Use the project's terms from `CONTEXT.md` if one exists.
- `docs/adr/` is shared with anyone else writing ADRs (other skills, humans), so numbering continues from whatever is there, regardless of who wrote it.
- If the new decision replaces an existing ADR, set the old one's status to `superseded by ADR-NNNN` instead of editing its content. If it settles the assumptions of a `proposed` ADR, update that ADR to `accepted` rather than writing a duplicate.

How the ledger maps onto an ADR:

- **One ADR per decision** that passes the format's three tests (hard to reverse, surprising without context, a real trade-off). If the talk produced several, write one each. If one fails the tests, tell the user in a sentence and skip it unless they still want it.
- **Only Confirmed content is written as the decision.** The ADR is what future readers and agents will treat as fact, so this is the last place an assumption can slip through.
- **Still-Assumed or Open items that the decision depends on** go in Consequences as one line each ("Assumes X; if wrong, Y changes"), and the ADR gets `status: proposed` instead of `accepted`. Low-stakes defaults don't belong in the ADR.
- **Constraints not visible in code** (legal, partner contracts, compliance) that came up in the talk are worth one sentence of context. They're the reason a future reader won't "fix" the decision.

Before finishing, check: nothing Assumed reads as settled, and the "why" cites the real reason from the talk, not a reconstructed one.
