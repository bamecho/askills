# Think behavior evaluation

Maintenance evidence for changes to `think`. Normal invocations do not load
this file.

## 2026-08-10: independent decision surface

An observed behavior problem motivated the change: independent `think` answers
described downstream skills, Handoff, approval routing, and a current delivery
slice even when the user asked only for a recommendation. The candidate changed
`think` alone. Bam runtime files remained unchanged for the promoted condition.

All candidate runs used isolated `agy --new-project` sessions with Gemini 3.6
Flash (High), high effort, and plan mode. The candidate never saw this rubric.
A fresh isolated run of the same model judged paired outputs under arbitrary
labels. One shell-corrupted batch-grill attempt was discarded and rerun with
the full prompt safely quoted.

| Organic request | Current output tokens | Final output tokens | Material result |
|---|---:|---:|---|
| Direct plugin-system decision | 659 | 473 | Same recommendation and premise; final removed the unrequested Delivery Slice section. |
| Explicit quiet-hours product spec | 768 | 719 | Both retained Target, Scope, Acceptance, premise, and the queued-message blocker. |
| Broad local-first collaboration direction | 1378 | 1049 | Final covered the requested solution without forced slices, downstream skills, or Next Steps. |
| Explicit dashboard-sharing spec and roadmap | 1055 | 691 | Final kept private links current and public links/analytics deferred without routing narration. |
| Keep/kill unused export preset | 324 | 353 | Both returned Kill; final added the premise that would change the verdict. |

The blinded judge scored current 46/50 and final 50/50. The two material current
failures were forced delivery slicing plus downstream skill routing on the broad
direction, and next-stage routing after the requested roadmap. The explicit spec
was a 10/10 tie, so making spec output conditional did not reduce its decision
surface in this run.

The quiet-hours specs were each handed to a fresh `batch-grill-me` run. Both
produced a complete first frontier (7 questions current, 6 final); the final did
not need an embedded Handoff or composition instruction.

A system regression then combined the final independent `think` with the
unchanged Bam Design playbook. It still selected private expiring links as the
current phase and kept public links and analytics deferred. A separate candidate
that also rewrote Bam's Design step emitted Workflow Progress, composed-skill,
and Next Steps narration, so that Bam wording change was rejected.

## Decision

- Promote the independent decision surface in `think`.
- Keep spec output conditional on user request or a durable multi-part decision.
- Keep Handoff, delivery-slice ownership, and downstream routing out of `think`.
- Leave Bam Design and its Handoff contract unchanged; they already own
  composition and retain current-slice behavior.
- Treat output-size changes as supporting data only. The promotion rests on
  request fidelity, preserved spec quality, the batch-grill handoff, and the Bam
  system regression.

These are single runs per condition on one model. Future changes should retain
the five prompts above and broaden model/run coverage when behavior is close.

## 2026-08-10: Wayfinder-inspired solution exploration

We tested whether a minimal spec needs an explicit instruction to identify its
load-bearing decision and explore materially different solution mechanisms.
Three organic prompts covered a multi-mechanism webhook design, an obvious JSON
preference change, and an encrypted collaborative whiteboard with an unresolved
revocation requirement. All runs used isolated `agy --new-project` sessions with
Gemini 3.6 Flash (High), high effort, and plan mode.

The first candidate told the model to explore mechanisms until one dominated or
the deciding tradeoff was clear. A blinded judge scored current 30/30 and the
candidate 21/30. The candidate hedged between inline and worker processing,
repeated a given constraint as a fragile premise, and left the whiteboard
recommendation conditional instead of choosing an honest default.

The second candidate moved comparison into private reasoning and required a
default unless no honest default survived a blocker. It fixed the hedging, but
current still won 30/30 to 26/30. The candidate leaked effort and execution-flow
detail, lost the JSON round-trip premise and useful close alternative on the
simple case, and only tied current on the hard whiteboard decision. Its useful
extra observation--plaintext already rendered on a client cannot be revoked by
cryptography alone--did not justify the cross-case regressions.

### Decision

- Reject both exploration instructions; leave runtime `think` unchanged.
- The current recommendation, close-alternative, and fragile-premise rules
  already explore a minimal spec proportionately on these cases.
- Keep large cross-session decision mapping outside `think`. Revisit runtime
  guidance only after a real run shows premature commitment to the first
  plausible mechanism.

## 2026-08-27: assumption ledger, opt-in document, load-bearing decomposition

Three behavior problems motivated this change. `think` could decide on its own
that a decision "needs a durable multi-part contract" and write a spec the user
never asked for. The review surface prescribed six named fields, which caps the
document at the template's shape rather than the decision's. And step 2 stated
the goal "keep the decision minimal" without a method for choosing what to cut,
so a tangled ask could return a broad answer whose parts nobody could check
independently.

The governing failure model is assumption capture: an agent meets an unknown,
picks the most plausible reading to keep moving, and that reading reaches
implementation. Code, tests, and dependent modules then all conform to it, which
proves internal consistency and never correctness. Every later exception patches
the distance between the assumption and reality until the complexity stops coming
from the problem. The countermeasure has to act while the assumption is still one
sentence, so it belongs in `think`, not downstream.

### Changes

- Frontmatter declares assumption visibility and load-bearing reduction.
- New `What the Decision Rests On` sorts every input into Confirmed, Open,
  Assumed, and Unexamined, and requires the latter two to be named. Added a Gate:
  no assumption reaches a recommendation unlabelled.
- Step 2 became `Cut to the load-bearing decision` with the method (find the one
  decision that determines the others), the reason (an unchecked part is where an
  assumption hides), and the escape hatch (`wayfinder`).
- The artifact is named a **decision record**, not a PRD or implementation spec.
  A PRD assumes requirements are known and a spec must be implementable; both
  pressure unknowns into settled values. A decision record treats decided and
  undecided as equals, so `not known yet` is a complete entry.
- `Review Surface` was replaced by `When You Write It Down`: no template, four
  content obligations, shape free.

### Structural evidence

Measured against the pre-change file with `grep`/`awk`:

| Metric | Before | After |
|---|---:|---:|
| Agent-discretion document triggers | 4 | 0 |
| Explicit denial of agent write authority | 0 | 1 |
| Prescribed template field names | 6 | 0 |
| Content obligations on a written document | 0 | 4 |
| Named decomposition method | 0 | 1 |
| `SKILL.md` initial load tokens | 1376 | 1837 |

The four discretion triggers were `When a spec is warranted` at lines 16, 27, and
40 plus `needs a durable multi-part contract` at line 18. All are gone; the only
authority is now a user request, with agent judgment explicitly denied.

### Route evidence

`trigger_eval.py` with `evals/trigger_cases.json` (12 should-trigger, 7
should-not-trigger, 3 near-neighbor) and `evals/semantic_config.json`:

| Threshold | Before recall | After recall | Precision (both) |
|---:|---:|---:|---:|
| 0.20 | 0.833 | **1.000** | 1.00 |
| 0.22 | 0.833 | **0.917** | 1.00 |
| 0.24 | 0.833 | 0.750 | 1.00 |
| 0.28 | 0.750 | 0.750 | 1.00 |
| 0.30 | 0.750 | 0.667 | 1.00 |

Boundary discipline did not regress: 7/7 should-not-trigger and 3/3 near-neighbor
pass at every threshold, zero false positives throughout. The `wayfinder`
near-neighbor still routes away, which was the risk of teaching `think` to talk
about decomposition.

The two assumption-surfacing prompts moved from 0.06 to 0.239 and from 0.06 to
0.221 — previously they matched nothing the description declared.

Aggregate recall above 0.24 is not comparable between the two descriptions.
`semantic_coverage` divides matched concept weight by the total weight the
description declares, so declaring the legitimate fourth capability
`surface_assumptions` raises the denominator from 0.76 to 1.00 and mechanically
lowers every single-concept prompt. Verified directly with
`desired_positive_concepts`. A first candidate that also carried the file policy
in the description scored worse still (0.667 at 0.28); the policy was moved to
the body, since where output lands is not a routing signal. Read the per-prompt
deltas and the boundary buckets, not the aggregate.

### Decision

- Promote the assumption ledger, the opt-in decision record, and the
  load-bearing cut.
- Accept +461 initial-load tokens. It buys the assumption gate, the decomposition
  method, and the removal of four self-authorized write paths; `context_sizer`
  reports no warning.
- Leave `bam-mode/playbooks/design.md` unchanged. It already says to keep the
  response in chat unless the user requests a file, so it is consistent with the
  stricter rule and still consumes the same in-chat decision surface.
- Not covered: this is static and route evidence only. No paired model runs were
  made for this change, so the earlier five behavior prompts remain the regression
  set for the next one. Whether the Unexamined category earns its cost against
  real transcripts is still unproven.

### Superseded later the same day

The user deleted the `bam-mode` orchestrator skill, so the note above about
leaving `bam-mode/playbooks/design.md` unchanged no longer describes a live file,
and the Handoff contract it referenced is gone with it. `think` itself did not
change. What replaced Handoff in the user's own flow is the Confirmed set that
survives a `grill` pass, and the downstream skills that used to read Handoff
Locked — `codebase-design`, `entity-model-design`, `ticket-tree` — were repointed
at upstream confirmed/locked decisions with an explicit rule that an assumed or
open item is not locked. Earlier entries are kept as written; they were true when
recorded.
