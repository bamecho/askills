# Durable Context Preflight

Shared preamble for every skill that reads optional memory or prior-decision context. Each `SKILL.md` links to this file and then adds skill-specific guidance.

## When to read durable context

Run the durable context steps only when one of these holds:

- The user mentions memory, preview, previous decisions, or a prior conclusion.
- The user provides a memory path.
- The current project exposes an obvious local memory summary (for example, a `MEMORY.md` or a documented memory directory).

Do not hard-code machine-specific memory roots, and do not read raw transcripts.

## Read order and budget

Read durable context in this order: user-provided path, current project scope, then global preferences. List titles first, then open at most 1-2 relevant summaries. Treat cross-project entries as transferable patterns only.

## Memory distillation redaction gate

When turning prior chats, durable memory, or cross-project notes into reusable Waza guidance, promote only workflow rules. Strip the following:
- Raw transcript text
- Screenshots
- Local paths
- Project-specific commands
- Issue or PR numbers
- Release tags
- Commit hashes
- Private product boundaries
- Paid or license details
- Support routing
- User names
- One-machine state

If an example is necessary, use neutral placeholders such as `ExampleCLI`, `ExampleApp`, `<issue>`, `<release>`, or `<command>`. Do not copy a private answer, maintainer reply, screenshot observation, or project-specific incident as a durable rule.

## Memory type mapping

- `decision`, `preference`, and `principle` are constraints for the current task (planning, design, review, debugging, voice, audit expectations, etc., depending on skill).
- `pattern` and `learning` are reusable checks or hypotheses.
- `fact` must be verified against current state before it affects the output.

Current code, diff, screenshots, logs, tests, docs, CI, remote state, and live probes always override memory. If they conflict with a remembered claim, name the conflict and follow current state.

Each skill adds its own paragraph below this reference for skill-specific overrides and constraints.
