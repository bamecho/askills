# Candidate prompt

The orchestrator passes this file to each Phase B candidate and fills in the variable inputs around it: the grounding brief path, the assigned design constraint, the isolated working directory, and the output path.

Spawn candidates with `subagent_type: generalPurpose` and no `model` argument. A candidate writes its proposal to its own output path, so the harness's built-in explore or readonly agent cannot be one. Arena's cross-judge is the one readonly agent in this flow; it only reads the candidates.

## Assigning the two constraints

Two candidates only explore different shapes when each gets a different structural constraint. The default pair:

- **Candidate 1 — minimize the interface.** One to three entry points; maximize capability per entry point.
- **Candidate 2 — optimize for the dominant caller.** Make the common path trivial, even when that concentrates more policy in the owner.

Substitute a pair when the grounding brief makes a different axis the real question, and name that axis before spawning. Common substitutions:

- **Seam placement.** One candidate puts the seam at the domain edge, the other pushes it to the transport edge.
- **Contested ownership.** Each candidate gives a disputed effect or piece of state to a different owner.
- **Port or no port.** One candidate introduces the port, the other proves the capability can be concentrated without it.

Two candidates on the wrong axis is a wasted run.

---

You are producing one candidate structure in codebase-design's parallel exploration. Read the grounding brief in full first. Its Locked decisions and surviving contracts are constraints, not suggestions — a candidate that reopens a locked product token is discarded.

Read [`design-vocabulary.md`](design-vocabulary.md) and use its terms exactly. The orchestrator compares candidates term by term; a candidate that says "service" where another says "module" cannot be compared.

Your output is a structure proposal, not an implementation. Write it to your assigned output path.

## What to produce

1. **Caller usage, written first.** Two or three real call sites in the caller's own code, from the scenarios in the grounding brief. The usage is the spec; derive everything else from it. When the usage and your declarations disagree, fix the declarations.
2. **Module map.** The fewest modules that each hide a coherent body of domain knowledge, with allowed dependency directions and the critical interactions between them. Compact ASCII in a fenced `text` block. Annotate it with the load-bearing state, effects, and recovery paths.
3. **Load-bearing declarations.** Exact repository-native declarations for new or changed boundaries only — those whose change would alter dependency direction, caller knowledge, state ownership, or failure semantics. Cite preserved contracts by path and symbol instead of reprinting them.
4. **Seam placement and dependency strategy.** For each seam, its category per [`dependency-categories.md`](dependency-categories.md), the adapters that justify it, and how the module is tested through it. Say "no new seam" when your shape needs none; a seam invented to fill this item is the hypothetical seam the orchestrator rejects.
5. **Rationale.** Which decisions are load-bearing and why. What complexity the public surface hides, and what it still exposes. The alternatives you considered inside your own shape and why you rejected them.

Declarations stop at type/interface declarations and function or method signatures. No executable bodies, no placeholder bodies, no pseudocode bodies, no copied function bodies — a placeholder body is implementation-shaped, not a contract. Use language-native syntax when it supports bodyless declarations; otherwise put conventional signatures in a `text` fence.

## Discipline the orchestrator scores you on

- **Interface depth.** Compare the capability hidden behind the public surface against the size of that surface. Prefer a simple interface that pulls complexity into the callee, even when the implementation gets less simple.
- **Data structures first.** Get the core types right and the rest follows. Trace each dominant access pattern through your structure; if the answer is "we add an index or a cache later", the structure is wrong.
- **No transport on the public surface.** Parse external data into domain types behind the interface. Storage schemas, framework objects, and wire formats stay private.
- **Explicit domain intent.** When one atomic write includes an effect consumed later (an event, an outbox row, a notification), the domain owner constructs the intent with its stable identity and semantic fields. Infrastructure translates and persists it. A semantic method name alone does not authorize an adapter to derive the effect.
- **Shared decision, separate effects.** When callers share a decision but perform different effects, expose the decision as domain data and keep each effect at its caller boundary. Mode flags, options, and callbacks must not make one capability own unrelated interactions.
- **Plans as data.** When one path previews or inspects the exact decisions another path executes, expose that plan as data: the preview caller owns presentation, the executor owns effects.
- **Shared state.** If two actors might both write, ask what happens. If the answer is not "nothing", default to per-actor state with a merge at the read boundary.
- **Invariants in types.** Hard-to-misuse types beat runtime checks, which beat prose comments. Validate at the boundary, trust types inside.
- **Idempotence.** For each state transition, say what happens if it runs twice or crashes halfway.
- **Short call chains.** If tracing a flow needs more than three files, flatten it.

Do not invent concurrency, locking, isolation, batching, or retry policy the grounding brief does not support with evidence. Say what you assumed instead.

## Divergence

You are one of two candidates, and your assigned constraint is deliberately narrower than "design this well". Follow it to its honest conclusion — including where it produces something the other candidate would not. Differences between candidates are the entire signal the orchestrator uses to pick a base and graft. Converging on a safe middle defeats the exploration and wastes both runs.

Where your constraint produces a genuinely bad result, say so in the rationale rather than quietly abandoning the constraint.
