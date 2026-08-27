# Design document

The single deliverable, written to `docs/design/NN-<slug>.md` — `NN` the next unused two-digit number in that directory, `<slug>` the same as the Phase A working directory. Optimize it for a one-minute architecture review by someone who will then hand it to an implementation agent: evidence earns space, ceremony does not. State each fact once.

Cite preserved contracts by path and symbol instead of reprinting them. Contain no executable, placeholder, or pseudocode bodies.

```md
# Design: <slice name>

## Decision
<Two or three sentences. The shape chosen and the one property that makes it the
right shape. A reader who stops here should know what is being built.>

## Caller usage
<The call sites from the winning candidate, in the caller's own code. This is
first because it is the spec. Two or three sites covering materially different
paths. Cite each site's `path:line` when the caller exists today; mark it `new
caller` when it does not. A reviewer cannot tell a traced call site from an
invented one otherwise.>

## Module map
<One compact ASCII view in a fenced `text` block: modules, allowed dependency
directions, critical interactions, annotated with load-bearing state, effects,
and recovery paths. Add a second sequence or state view only when the first view
cannot make a load-bearing failure or transition clear.>

## Contracts
<Exact repository-native declarations for new or changed load-bearing boundaries
only. Declarations stop at type/interface declarations and function or method
signatures.

Beside a contract, add a **Tech Note** (2-3 lines) only for evidence-backed
semantics needed to approve state, effects, transactions, concurrency, external
calls, or failures. Leave unrelated implementation policy open.

Mark a contract `inferred` when its shape came from the design rather than from
a traced caller or an existing declaration, and name what would confirm it. An
inferred contract and a traced one are indistinguishable once written down, and
the inferred one is where implementation deviates.>

## Preserved contracts
<Path, symbol, and compatibility requirement for each surviving public contract
the slice touches. One line each.>

## Seams
<One line per seam: its location, its dependency category, the adapters that
justify it, and how the module is tested through it. Omit the section when the
design introduces no new seam — a shape that correctly avoided one must not be
pressured into inventing a seam to fill this heading.>

## What each caller must know
<Per caller: the coordination, representation, or policy this shape adds to or
removes from it. Removal is the depth claim, stated concretely enough to check.
Say so plainly when a caller's required knowledge is unchanged; a shape that
adds a capability without taking knowledge away is a legitimate outcome, not a
gap to talk around.>

## Synthesis record
<Which candidate became the base and why. What was grafted from the other and
why. What was rejected and why — the rejections are the highest-signal part for
a future reader. Note convergence when both candidates reached the same shape,
and dropouts when a candidate failed to produce output.>

## Tradeoffs accepted
<One bullet per tradeoff, as "we accept X in exchange for Y". Name anything a
future reader could mistake for an oversight. Include a tradeoff only when
approval depends on seeing it.>

## Implementation guidance
<The part an implementation agent is handed. Cover:
- Which contracts are load-bearing and must not be silently redesigned. Name
  them.
- What is deliberately left open for the implementer to decide.
- Where to start: the first contract to fill in.
- What a deviation means: if a signature needs a parameter this document did not
  anticipate, surface it rather than absorbing it. Repeated deviations of the
  same shape mean the design is wrong, not the implementation.

Ordering, verification, tickets, and stop conditions belong to plan, not here.>

## Blockers
<Real unresolved decisions that prevent approval, each with its impact and who
owns it. Adapter requirements are not blockers. Omit the section when there are
none.>
```

## Rules

- **Write the document in the user's language.** Chinese when the user works in Chinese. Prose, rationale, and section content are translated; headings, declarations, symbol names, paths, and the module map's labels stay as they are, because they are the artifact implementation is matched against.
- **No alternatives section.** The synthesis record already says what lost and why. Add a named-alternatives section only when the user has explicitly asked to choose among viable shapes.
- **No rubric compliance.** Never write that the shape passed the shape review. Report the decision the review produced.
- **End at blockers.** Plan owns implementation ordering and verification steps.
- **A stale proposal or a human-corrected shape is evidence, not an alternative.** Do not list it as a candidate that lost.
