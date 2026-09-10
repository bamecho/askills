# Skills

A collection of methodology-driven skills for effective software development with AI assistants.

## Core Skills

### Decision & Planning

- **talk** - Turn rough ideas into evidence-grounded decisions through iterative dialogue. Use for planning direction, scope judgment, architecture choices, and feasibility analysis.

- **shape** - Design codebase structure before implementation. Explores two distinct approaches in parallel and synthesizes one design doc. Use when ownership, interfaces, or seams need to change.

- **wayfinder** - Plan large work as a shared map of decision tickets. Resolve them one at a time until the path forward is clear.

### Methodology

- **method** - Apply specific methodologies to your work. Includes First Principles, Adversarial Review, Ablation Experiment, Occam's Razor, List Uncertainties, Independent Thinking, Critical Thinking, and High Cohesion/Low Coupling. Methods can be combined.

- **grilling** - Stress-test plans, decisions, or ideas through relentless questioning. Use when you want to probe assumptions and edge cases.

### Research & Investigation

- **research** - Investigate questions against high-trust primary sources and capture findings as Markdown. Use when you need docs, API facts, or reading legwork delegated to a background agent.

- **grill-with-docs** - Research with document grounding for deeper questioning.

### Code Quality

- **tdd** - Test-driven development workflow
- **domain-modeling** - Domain-driven design patterns
- **design-verifiable-slices** - Break work into verifiable increments
- **typescript-best-practices** - TypeScript conventions and patterns
- **no-comments** - Write self-documenting code
- **unslop** - Remove unnecessary verbosity

### Utilities

- **bro** - Casual, helpful assistant mode

## Usage

Skills are invoked with `/<skill-name>` in your AI assistant. For example:

```
/talk Should we use microservices or monolith for this project?
```

```
/method Apply First Principles and Occam's Razor to this architecture decision
```

```
/grilling Review my API design
```

## Acknowledgments

Inspired by and built upon ideas from:
- [mattpocock/skills](https://github.com/mattpocock/skills)
- [cursor/pstack](https://github.com/cursor/pstack)
- [tw93/waza](https://github.com/tw93/waza)
