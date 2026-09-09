---
description: Route task to appropriate methodology from 8 agent-effectiveness principles
mode: production
---

# Agent Method Router

## Purpose

When user describes a problem or task, analyze its characteristics and route to 1-3 applicable methodologies. Guide subsequent workflow with selected principles.

## 8 Methodologies

1. **First Principles** → Problem unclear; verify existence, root cause, reproducibility
2. **Adversarial Review** → Critical decisions; independent agent finds counter-examples
3. **Ablation Experiment** → Validate necessity; remove component, test impact
4. **Occam's Razor** → New problem; simplest working version first
5. **List Uncertainties** → Incomplete info; explicit gaps, untested scenarios, speculation
6. **Independent Thinking** → Multi-agent; separate judgment before discussion
7. **Critical Thinking** → Verify conclusions; distinguish facts from inference
8. **High Cohesion, Low Coupling** → Architecture/refactor; clear module boundaries

## Routing Logic

**Input:** User task description

**Process:**
1. Identify task type (debug, design, implement, review, research, refactor)
2. Match characteristics to methodologies (see [Routing Matrix](references/routing-matrix.md))
3. Select 1-3 most relevant methods
4. Sequence them in workflow order

**Output:**
- Task analysis (1-2 sentences)
- Selected methods with rationale
- Workflow steps applying each method
- Uncertainties/assumptions requiring clarification

## Quick Routing

| Task Pattern | Primary Method | Secondary |
|--------------|----------------|-----------|
| "Fix bug X" | First Principles | Critical Thinking |
| "Design feature Y" | Occam's Razor | Adversarial Review |
| "Is X necessary?" | Ablation Experiment | Critical Thinking |
| "Review implementation" | Adversarial Review | Critical Thinking |
| "Refactor module" | High Cohesion, Low Coupling | Ablation Experiment |
| "Multi-agent task" | Independent Thinking | Adversarial Review |
| "Unclear requirements" | First Principles | List Uncertainties |
| "Optimize performance" | Ablation Experiment | First Principles |

## Application Style

- **Lean:** Apply principles as concise checks, not lengthy procedures
- **Evidence-based:** Request facts before conclusions
- **Explicit:** Surface assumptions and unknowns
- **Iterative:** Start simple, add complexity only when proven necessary

## References

- [Method Catalog](references/method-catalog.md) — detailed principle descriptions
- [Routing Matrix](references/routing-matrix.md) — task characteristics → method mapping
