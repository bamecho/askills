# Shape review

Screen every candidate against this lens before synthesis, and screen the synthesized shape again after grafting. A flag is a reason to revise or reject a shape, not a section to report on. The design document reports the resulting decision, never rubric compliance.

Uses the vocabulary in [`design-vocabulary.md`](design-vocabulary.md).

## Shallow module

The public surface exposes nearly as much coordination or policy as the module hides. Callers must sequence several methods, select internal stages, or learn implementation rules to complete one capability.

Signs:

- Callers coordinate several calls to complete one operation.
- Public options expose internal stages or implementation choices.
- Learning the interface does not save the caller from learning the implementation.

Fix: concentrate the capability behind a smaller semantic contract.

## Information leakage

Several modules depend on the same private representation, provider protocol, storage schema, or policy decision, so changing it requires coordinated edits. Public re-exports of transport or wire types are leakage.

Fix: keep boundary representations private and translate them into the concepts owned by the receiving module. Share domain data and policy — not caller-specific presentation or transport projection. Formatting and wire mapping stay at the caller edge unless they *are* the shared domain capability.

## Temporal decomposition

Modules are organized as execution stages — load, validate, transform, save — even though they share one body of domain knowledge and repeat one representation across boundaries.

Fix: group by owned knowledge and invariants. Execution order belongs in the interaction flow, not the module map. Methods that run at different times can belong to one module when they protect the same decisions.

## Pass-through boundary

A layer forwards the same arguments and result without adding policy, translation, ownership, or isolation.

Fix: apply the deletion test. Remove it, or move the complete capability to the boundary that can own it. Keep a forwarding boundary only when it adds policy, adaptation, or a distinct abstraction.

## Policy displacement

An adapter or infrastructure implementation must inspect domain state, infer a transition, or invent an effect because its interface is too generic.

Fix: keep the representation hidden, but name the semantic operation or pass an explicit domain intent so infrastructure only executes the required effect. For an effect consumed later, the owner supplies the intent's stable identity and semantic fields; the adapter may translate and persist them, never derive them.

## Hypothetical seam

A port exists with exactly one adapter and no second one in prospect. That is indirection wearing a seam's clothes.

Fix: inline it until something actually varies across it. Production plus test is a real pair; production alone is not.

## Contract drift

An interaction label in the module map claims a flow returns, accepts, or observes a value the declarations do not provide. The diagram and the contracts disagree.

Fix: reconcile every interaction label against the preserved and proposed declarations. The map and the declarations are one artifact and must agree.
