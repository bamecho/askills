# Design vocabulary

Use these terms exactly. Two candidate designs are only comparable when both name the same things the same way, so do not substitute "component", "service", "API", or "boundary".

## Terms

**Module** — anything with an interface and an implementation. Deliberately scale-agnostic: a function, a class, a package, or a tier-spanning slice. A module is a responsibility boundary, not necessarily a file, layer, entity, or execution stage. _Avoid_: unit, component, service.

**Interface** — everything a caller must know to use the module correctly. Not just the type signature: also invariants, ordering constraints, error modes, required configuration, and performance characteristics. _Avoid_: API, signature (both too narrow — they name only the type-level surface).

**Implementation** — what sits inside a module. Distinct from **adapter**: a thing can be a small adapter with a large implementation (a Postgres repository) or a large adapter with a small implementation (an in-memory fake). Say "adapter" when the seam is the topic, "implementation" otherwise.

**Depth** — leverage at the interface: how much behavior a caller or test can exercise per unit of interface it has to learn. A module is **deep** when a large amount of behavior sits behind a small interface, **shallow** when the interface is nearly as complex as the implementation.

**Seam** — a place where behavior can be altered without editing in that place; the *location* at which a module's interface lives. Where to put the seam is its own decision, separate from what goes behind it. _Avoid_: boundary (overloaded with DDD's bounded context).

**Adapter** — a concrete thing satisfying an interface at a seam. Names a *role* (which slot it fills), not substance (what is inside it).

**Leverage** — what callers get from depth: more capability per unit of interface learned. One implementation pays back across N call sites and M tests.

**Locality** — what maintainers get from depth: change, bugs, knowledge, and verification concentrate in one place instead of spreading across callers. Fix once, fixed everywhere.

## Principles

**Depth is a property of the interface, not the implementation.** A deep module can be internally composed of small swappable parts; they just are not part of its interface. A module may have internal seams (private to its implementation, used by its own tests) as well as the external seam at its interface. Do not expose an internal seam through the interface merely because a test uses it.

**Do not confuse a deep module with a deep call chain.** A deep call chain scatters understanding across layers. A deep module concentrates capability behind one interface.

**The deletion test.** Imagine deleting the module. If complexity vanishes, it was a pass-through. If complexity reappears across N callers, it was earning its keep.

**The interface is the test surface.** Callers and tests cross the same seam. If a test needs to reach *past* the interface, the module is the wrong shape.

**One adapter means a hypothetical seam; two means a real one.** Do not introduce a seam unless something actually varies across it — typically production plus test. A single-adapter seam is indirection.

**Single source of truth per invariant.** Derive instead of synchronizing.

## Rejected framings

- **Depth as a ratio of implementation lines to interface lines.** Rewards padding the implementation. Use depth-as-leverage instead.
- **"Interface" as a language `interface` keyword or a class's public methods.** Too narrow; interface here includes every fact a caller must know.
- **"Boundary".** Overloaded with DDD's bounded context. Say **seam** or **interface**.
