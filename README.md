# Myelin Skills

A comprehensive skill library for AI-assisted software development, focusing on clear thinking, structured design, and systematic verification.

## 🎯 Philosophy

This skill library emphasizes:
- **Evidence over assumptions** - Ground decisions in real code, docs, and data
- **Clear communication** - Use ASD-STE100 style (simple words, ≤20 words per sentence)
- **Systematic thinking** - From problem understanding to structure design to verification
- **Progressive disclosure** - Surface decisions when they matter, not before

## 📚 Skill Categories

### Problem Understanding

Start here when requirements are unclear or assumptions are hidden.

- **talk** - Turn rough ideas into evidence-grounded decisions through dialogue. Surfaces hidden assumptions before they become code.
- **clarify** - Restate your last message in plain language, like one human talking to another.
- **find-core-problem** - Analyze implicit needs and contradictions to find the core problem.
- **restate-goal** - Restate what you think the goal is and what problem needs solving.
- **grilling** - Stress-test plans through relentless questioning. Maps decisions as a tree and works the frontier in rounds.

### Structure Design

Design interfaces and ownership before writing code.

- **shape** - Design codebase structure before implementation. Explores two approaches in parallel, synthesizes one design doc.
- **domain-modeling** - Domain-driven design patterns and modeling.
- **wayfinder** - Plan large work as a shared map of decision tickets in `docs/wayfinder/`. Resolve one at a time until the path is clear.

### Verification & Quality

Systematic verification from unit tests to end-to-end proof.

- **proving-changes** - Prove that fixes and features work end-to-end. Reproduces failures, exercises real paths, collects independent evidence.
- **verification-design** - Design verification strategy for complex changes.
- **create-verification-skill** - Generate custom verification skills for specific scenarios.
- **maintain-verification-skill** - Update and improve existing verification skills.
- **tdd** - Test-driven development workflow.
- **method** - Apply specific methodologies: First Principles, Adversarial Review, Ablation Experiment, Occam's Razor, List Uncertainties, Independent Thinking, Critical Thinking, High Cohesion/Low Coupling.

### Documentation & Communication

Write clear explanations and documentation.

- **explaining-code** - Explain code, systems, and changes with diagrams, pseudocode, call trees, and diffs.
- **writing-prompts** - Write system prompts, tool descriptions, and skill instructions that models can follow.
- **plain-writing** - Write clear prose: simple words, complete sentences, no jargon, 25 specific rules.
- **answer-me-with-html** - Turn complex answers into visual HTML explainers with diagrams and interactive elements.
- **creating-artifacts** - Create shareable HTML pages (plans, proposals, reports) that others can review and comment on.
- **creating-views** - Render interactive HTML views inline: charts, dashboards, explorable diagrams, UI mockups.

### Skill Development

Build and manage skills themselves.

- **building-skills** - Create well-structured skills: naming, frontmatter, progressive disclosure, MCP integration.
- **building-agents** - Create custom agents with specific instructions, tools, and MCP servers.
- **building-schedules** - Set up schedules for reminders and recurring checks.

### Research & Investigation

Delegate research to background agents.

- **research** - Investigate questions against primary sources, capture findings as Markdown.
- **grill-with-docs** - Research with document grounding for deeper questioning.

### Code Standards

Maintain consistent code quality.

- **typescript-best-practices** - TypeScript conventions and patterns.
- **no-comments** - Write self-documenting code.
- **unslop** - Remove unnecessary verbosity from code and prose.

## 🚀 Usage

Skills are invoked with `/<skill-name>` in your AI assistant:

```
/talk Should we use microservices or monolith?
```

```
/shape Design the authentication module structure
```

```
/proving-changes Prove the retry fix works end-to-end
```

```
/answer-me-with-html Explain how the event loop works
```

## 🔄 Typical Workflows

### Planning a Feature
1. `/talk` - Understand the goal and surface assumptions
2. `/shape` - Design the structure
3. `/proving-changes` - Verify end-to-end

### Debugging
1. `/find-core-problem` - Identify the real issue
2. `/explaining-code` - Document the current behavior
3. `/proving-changes` - Prove the fix works

### Documentation
1. `/explaining-code` - Generate technical explanation
2. `/plain-writing` - Clean up the prose
3. `/creating-artifacts` - Create shareable page if needed

## 📖 Language Style

All skills follow **ASD-STE100** principles:
- Simple words, ≤20 words per sentence
- One meaning per sentence
- No abstract terms (say "cache the result" not "approach")
- No vague phrases (say "cut query time from 800ms to 50ms" not "optimize performance")
- Use consistent terms throughout
- Be concrete about what actually happens

## 🙏 Acknowledgments

Built upon ideas from:
- [mattpocock/skills](https://github.com/mattpocock/skills)
- [cursor/pstack](https://github.com/cursor/pstack)
- [tw93/waza](https://github.com/tw93/waza)

Additional skills adapted from Claude Code's built-in skill library.
