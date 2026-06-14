# Codex AGENTS.md Boilerplate

A production-oriented `AGENTS.md` and Codex Skills boilerplate for building apps and services with Codex.

This template is designed for both:

- new projects that should start with disciplined AI-assisted development workflows
- existing projects that started casually and now need stronger development guardrails

The core idea is simple:

> Do not let Codex implement vague human instructions directly.  
> First rewrite the request into an explicit engineering task, then delegate investigation, implementation, and review to structured subagents.

## What This Provides

This repository contains:

```txt
.
├── AGENTS.md
├── README.md
├── examples
│   ├── greenfield-prompt.md
│   └── existing-project-prompt.md
└── skills
    ├── instruction-rewriter
    │   └── SKILL.md
    └── subagent-orchestrator
        └── SKILL.md
```

## Goals

This boilerplate helps Codex:

- start from Plan Mode for non-trivial work
- rewrite abstract human prompts into explicit task specs
- use subagent-style delegation for investigation, implementation, and review
- preserve small, reviewable diffs
- avoid speculative implementations
- avoid accidental dependency, schema, auth, payment, or infrastructure changes
- enforce security and privacy constraints
- reduce project-level token cost by preventing large wrong turns
- make final results easier for humans to review

## Why Plan Mode First?

In app and service development, the expensive part is usually not code generation itself. The expensive part is:

- generating the wrong architecture
- rewriting large scaffolds
- correcting misunderstood requirements
- undoing unnecessary dependencies
- fixing type and test failures after the fact
- re-reading a bloated codebase on every follow-up task

Plan Mode reduces total cost by forcing Codex to define:

- goal
- assumptions
- constraints
- non-goals
- acceptance criteria
- verification plan
- approval gates

before implementing.

This may increase the first response size, but it usually reduces total project cost over time.

## Intended Workflow

For non-trivial tasks, Codex should follow this flow:

```txt
Human request
  ↓
Instruction Rewriter Skill
  ↓
Explicit engineering task
  ↓
Plan Mode
  ↓
Subagent Orchestrator Skill
  ↓
Investigator / Implementer / Reviewer workstreams
  ↓
Main agent integration
  ↓
Summary, verification, risks, follow-ups
```

## Recommended Use

Copy these files into your project root:

```txt
AGENTS.md
skills/instruction-rewriter/SKILL.md
skills/subagent-orchestrator/SKILL.md
```

Then fill in the `Repository-Specific Overrides` section in `AGENTS.md`.

At minimum, specify:

```md
## Project Stack
- Language:
- Framework:
- Package manager:
- Database:
- Auth:
- Payments:
- Hosting:
- Test framework:
- Lint:
- Typecheck:
- Build:

## Commands
- Install:
- Dev:
- Test:
- Test single file:
- Lint:
- Typecheck:
- Build:
- Format:
- Database migration:
- Database seed:
```

## For New Projects

Use `examples/greenfield-prompt.md` before asking Codex to scaffold a full application.

The recommended approach is:

1. define scope
2. define non-goals
3. propose architecture
4. propose dependencies
5. propose implementation phases
6. implement a minimal vertical slice
7. verify each phase before continuing

Avoid asking Codex to generate an entire SaaS in one pass.

## For Existing Projects

Use `examples/existing-project-prompt.md` after adding this template to an existing repository.

The recommended first task is not implementation. It is repository discovery.

Ask Codex to inspect the project and propose updates to the `Repository-Specific Overrides` section.

## Important Notes

This template intentionally uses strong defaults.

For very small tasks, the full process may be too heavy. `AGENTS.md` allows trivial tasks such as typo fixes, small copy edits, and minor documentation changes to skip full subagent delegation.

For high-risk work, Codex must stop and request human approval before proceeding.

High-risk work includes:

- production dependency changes
- database migrations
- authentication or authorization changes
- payment or billing changes
- secret handling
- infrastructure or deployment changes
- destructive operations
- breaking public API changes

## License

MIT. Adjust as needed for your repository.
