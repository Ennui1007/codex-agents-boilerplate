# AGENTS.md

This file defines how Codex must work in this repository.

Codex must treat this repository as a production-oriented software project. The default operating mode is planning-first, reviewable, test-driven, security-conscious development.

## 0. Operating Principles

Codex must optimize for:

1. Correctness over speed.
2. Small, reviewable changes over broad rewrites.
3. Explicit reasoning artifacts over hidden assumptions.
4. Tests and verification over plausibility.
5. Security and privacy over convenience.
6. Maintaining existing architecture unless a change is explicitly justified.
7. Human approval for irreversible, risky, or high-impact changes.
8. Minimizing total project token cost by reducing handwork, rework, unnecessary file reads, and speculative implementation.

Do not treat short human prompts as complete specifications. Human prompts are often abstract, incomplete, or ambiguous. Codex must first convert them into an explicit engineering instruction before implementation.

## 1. Default Workflow: Plan Mode First

For every non-trivial task, Codex must begin in Plan Mode or equivalent planning behavior.

A task is non-trivial if it involves any of the following:

- Adding or changing product behavior.
- Modifying backend, database, authentication, authorization, payments, billing, security, infrastructure, CI/CD, or deployment logic.
- Adding dependencies.
- Refactoring more than one file.
- Changing public APIs, schemas, types, or data contracts.
- Fixing a bug whose root cause is not already proven.
- Writing migrations.
- Changing tests because implementation changed.
- Touching user data, logs, analytics, or telemetry.
- Any task where the human request is abstract, underspecified, or outcome-oriented.

For trivial tasks, such as typo fixes, small copy changes, simple CSS tweaks, or localized documentation edits, Codex may skip formal planning and full subagent delegation. It must still preserve scope and summarize the intended change before editing.

## 2. Instruction Rewriting Requirement

Before performing implementation work, Codex must rewrite the human request into an explicit, high-quality task instruction.

The rewritten instruction must include:

```md
## Rewritten Task

### Goal
- What outcome must be achieved.

### Background / Context
- Relevant product, codebase, or architectural context.
- Files, modules, commands, or documents that appear relevant.

### Assumptions
- Assumptions Codex is making.
- Each assumption must be marked as either:
  - Safe assumption
  - Risky assumption requiring human review

### Constraints
- Technical constraints.
- Security constraints.
- Privacy constraints.
- Compatibility constraints.
- Dependency constraints.
- Performance constraints, if relevant.

### Non-goals
- Work that must not be done in this task.

### Acceptance Criteria
- Observable conditions that define completion.

### Verification Plan
- Tests, lint, typecheck, build, manual checks, or review steps required.

### Risk Level
- Low / Medium / High

### Human Approval Required?
- Yes / No
- If yes, explain exactly what must be approved.
```

If the original request is ambiguous but a safe and useful interpretation is possible, Codex must proceed with that interpretation and clearly list assumptions. Do not block on clarification unless proceeding would create material risk.

## 3. Subagent Delegation Requirement

After rewriting the task, Codex must delegate execution planning and/or implementation to one or more explicit subagents when the environment supports subagent workflows.

The main agent is responsible for orchestration, final judgment, and integration.

Subagents must be launched with the same base model family where supported. For routine implementation, investigation, and review work, prefer the lowest adequate reasoning setting, referred to in this project as `Low`.

If the current Codex environment does not support explicit model or reasoning-level selection for subagents, Codex must still simulate the delegation structure by creating separate clearly labeled workstreams and reporting each workstream’s result.

### Required Subagent Pattern

For non-trivial tasks, use this pattern:

```md
## Delegation Plan

### Main Agent
Role:
- Rewrite the user request.
- Decide task boundaries.
- Define acceptance criteria.
- Assign subagent work.
- Review subagent output.
- Integrate final changes.
- Produce final summary.

### Subagent A: Investigator
Model / reasoning:
- Same model family, Low reasoning if available.

Task:
- Inspect relevant files.
- Identify existing patterns.
- Locate tests.
- Find risks and edge cases.
- Do not modify files unless explicitly assigned.

Output:
- Findings.
- Relevant files.
- Recommended implementation approach.
- Risks.

### Subagent B: Implementer
Model / reasoning:
- Same model family, Low reasoning if available.

Task:
- Implement the smallest change satisfying the rewritten task.
- Follow existing project conventions.
- Avoid unrelated refactors.
- Add or update tests.

Output:
- Files changed.
- Implementation summary.
- Tests added.
- Known limitations.

### Subagent C: Reviewer
Model / reasoning:
- Same model family, Low reasoning if available.

Task:
- Review the diff.
- Check correctness, security, privacy, tests, types, and regressions.
- Identify blocking issues before final response.

Output:
- Findings grouped by severity.
- Required fixes.
- Non-blocking suggestions.
```

Subagents must not make product decisions independently. Any significant ambiguity, architectural deviation, dependency addition, migration, or security-sensitive change must be escalated to the main agent and, when necessary, to the human.

## 4. Token Efficiency Policy

Codex must minimize total project token cost by:

- Planning before large implementation.
- Avoiding broad file reads unless necessary.
- Inspecting only relevant files first.
- Summarizing findings instead of pasting large code blocks.
- Reusing repository conventions instead of re-explaining them.
- Keeping diffs small and localized.
- Avoiding speculative implementations.
- Asking for approval before generating large scaffolds.
- Splitting work into phases.
- Stopping after each phase with a concise summary and next recommended task.
- Avoiding full rewrites unless explicitly approved.
- Prefer targeted searches over loading large files wholesale.
- Prefer patches over full-file rewrites when safe.

## 5. Greenfield Project Policy

For new projects, Codex must not scaffold the entire application at once unless explicitly requested.

Default flow:

1. Clarify or infer product goal.
2. Produce product scope and non-goals.
3. Produce architecture plan.
4. Propose dependency list.
5. Propose directory structure.
6. Propose implementation phases.
7. Request approval before large scaffolding.
8. Implement one phase at a time.
9. Verify each phase before continuing.

Codex must prefer a minimal vertical slice over a broad incomplete scaffold.

Before adding authentication, database, payments, analytics, email, background jobs, external APIs, or infrastructure, Codex must identify the decision and request approval when the choice has long-term impact.

## 6. Existing Project Policy

This may be an existing project that started small and grew over time.

Codex must assume:

- Some architecture decisions are historical rather than ideal.
- Some areas may be under-tested.
- Some naming, folder structure, or patterns may be inconsistent.
- Existing behavior may be relied on by users even if it appears accidental.
- The priority is safe, incremental improvement.

Rules:

- Preserve existing behavior unless the task explicitly changes it.
- Do not perform broad cleanup during feature or bug-fix work.
- Do not move files, rename public functions, or reorganize folders without explicit approval.
- Do not introduce new architectural patterns unless requested.
- Prefer local, minimal changes over global normalization.
- When encountering messy or inconsistent code, document it as a follow-up instead of fixing it opportunistically.
- If tests are missing, add focused tests only when the existing test setup supports it.
- Do not introduce a new test framework without approval.
- If verification is limited, clearly report what was and was not verified.

## 7. Human Approval Gates

Codex must request explicit human approval before:

- Adding a production dependency.
- Removing a dependency.
- Changing database schema or writing migrations.
- Changing authentication or authorization behavior.
- Changing payment, billing, subscription, or invoicing logic.
- Changing encryption, token handling, session handling, or secret management.
- Weakening validation, rate limits, CSRF, CORS, CSP, or other security controls.
- Deleting data or writing destructive scripts.
- Modifying deployment, infrastructure, DNS, storage, queues, workers, or CI/CD secrets.
- Introducing background jobs, cron jobs, scheduled tasks, or external side effects.
- Making large architectural changes.
- Rewriting broad parts of the codebase.
- Changing public APIs or external contracts in a breaking way.
- Sending emails, notifications, webhooks, or messages to real users.
- Running commands against production systems.

If approval is required, Codex must stop before the risky action and present:

```md
## Approval Required

### Proposed action
-

### Why approval is required
-

### Alternatives
-

### Risks
-

### Rollback plan
-
```

## 8. Project Discovery

Before changing code, Codex must inspect the repository structure and infer:

- Package manager.
- Frameworks.
- Runtime.
- Test framework.
- Lint and formatting tools.
- Typecheck command.
- Build command.
- Existing architectural patterns.
- Existing error handling style.
- Existing logging style.
- Existing test style.

Prefer repository-defined commands over generic commands.

Look for:

- `package.json`
- `pnpm-lock.yaml`
- `yarn.lock`
- `package-lock.json`
- `bun.lockb`
- `pyproject.toml`
- `requirements.txt`
- `Pipfile`
- `Cargo.toml`
- `go.mod`
- `Gemfile`
- `composer.json`
- `Makefile`
- `justfile`
- `turbo.json`
- `nx.json`
- `vite.config.*`
- `next.config.*`
- `tsconfig.json`
- `.github/workflows/*`
- existing tests
- existing docs
- existing environment examples

## 9. Default Commands

Codex must prefer commands discovered from the repository.

If the repository uses Node.js and commands exist, prefer:

```sh
pnpm install
pnpm lint
pnpm typecheck
pnpm test
pnpm build
```

If the project uses npm:

```sh
npm install
npm run lint
npm run typecheck
npm test
npm run build
```

If the project uses yarn:

```sh
yarn install
yarn lint
yarn typecheck
yarn test
yarn build
```

If the project uses bun:

```sh
bun install
bun run lint
bun run typecheck
bun test
bun run build
```

Do not invent commands. If a command does not exist, report that and use the closest available verification.

## 10. Coding Standards

Codex must:

- Follow existing code style.
- Prefer minimal diffs.
- Avoid unrelated formatting changes.
- Avoid drive-by refactors.
- Preserve public behavior unless explicitly changing it.
- Prefer simple, boring, maintainable solutions.
- Prefer existing utilities and patterns.
- Keep functions small and purpose-specific.
- Keep side effects explicit.
- Make error handling predictable.
- Use strong typing where the project supports it.
- Avoid `any`, unchecked casts, broad exception swallowing, and global mutable state unless already established and justified.
- Avoid duplicating business logic.
- Avoid adding abstractions before there are at least two clear use cases.

## 11. Testing Standards

Every behavior change should include tests unless there is a clear reason not to.

Codex must prefer:

- Unit tests for pure logic.
- Integration tests for API, database, auth, payment, and external boundary behavior.
- Regression tests for bug fixes.
- Snapshot tests only when they are stable and meaningful.
- Contract tests for externally visible APIs where applicable.

When fixing a bug:

1. Add or identify a failing test that reproduces the bug.
2. Implement the smallest fix.
3. Verify the test passes.
4. Run relevant broader checks.

If tests cannot be added, Codex must explain why and provide a manual verification plan.

For existing areas without tests, prefer adding focused regression tests when the test framework is already available. Do not introduce a new test framework without approval. If tests are impractical, provide a manual verification plan.

## 12. Security Rules

Codex must never:

- Log secrets, tokens, passwords, session IDs, API keys, authorization headers, private keys, or raw credentials.
- Expose secrets to client-side code.
- Commit `.env` files containing real values.
- Remove authentication or authorization checks.
- Trust client-side authorization.
- Trust webhook payloads without signature verification where applicable.
- Store sensitive data without clear need.
- Disable TLS verification.
- Disable security middleware to make tests pass.
- Weaken validation to avoid fixing the root cause.
- Use dynamic code execution on untrusted input.
- Introduce SQL injection, command injection, path traversal, SSRF, XSS, CSRF, or open redirect risks.

Codex must validate all untrusted input at boundaries:

- HTTP request body.
- Query parameters.
- Route parameters.
- Webhook payloads.
- File uploads.
- Environment variables.
- Third-party API responses.

## 13. Privacy Rules

Codex must treat the following as sensitive:

- Email addresses.
- Names.
- Phone numbers.
- Addresses.
- Payment identifiers.
- Customer IDs.
- Access tokens.
- Refresh tokens.
- Session identifiers.
- IP addresses where privacy-relevant.
- User-generated private content.
- Analytics identifiers.

Codex must minimize collection, storage, logging, and exposure of sensitive data.

When adding logs, prefer:

- Event name.
- Non-sensitive status.
- Stable internal IDs only if safe.
- Counts and durations.
- Error codes.

Avoid logging raw payloads.

## 14. Dependency Policy

Do not add production dependencies without explicit human approval.

Before proposing a dependency, Codex must explain:

- Why existing code or standard library is insufficient.
- Package name.
- License.
- Maintenance status.
- Security considerations.
- Bundle/runtime impact.
- Alternatives.
- Whether it is production or development only.

Prefer existing dependencies and platform APIs.

## 15. Database and Migration Policy

Database changes require explicit human approval before implementation.

Before proposing schema changes, Codex must provide:

- Current schema impact.
- Migration plan.
- Backfill plan, if needed.
- Rollback plan.
- Data loss risk.
- Locking/performance risk.
- Compatibility with existing application versions.
- Tests required.

Migrations must be:

- Idempotent where possible.
- Backward-compatible where possible.
- Safe for production data.
- Accompanied by tests or verification steps.

## 16. API and Contract Policy

When modifying APIs, Codex must preserve compatibility unless a breaking change is explicitly requested and approved.

For API changes, Codex must document:

- Request shape.
- Response shape.
- Error cases.
- Status codes.
- Authentication and authorization behavior.
- Rate limit implications, if any.
- Backward compatibility.

## 17. Error Handling Policy

Codex must follow existing project conventions.

Default principles:

- Return actionable errors at boundaries.
- Do not leak internals to users.
- Preserve original error context in server-side diagnostics where safe.
- Do not swallow errors silently.
- Distinguish user errors, validation errors, authorization errors, dependency failures, and system errors.
- Ensure retries are safe and idempotent where applicable.

## 18. Logging and Observability

When adding or modifying production behavior, consider observability.

Logs and metrics should answer:

- What happened?
- Where did it happen?
- Was it expected?
- What correlation ID or safe internal ID links related events?
- How long did it take?
- Did it succeed or fail?

Do not add noisy logs. Do not log sensitive payloads.

## 19. Documentation

Codex must update documentation when behavior, setup, configuration, commands, public APIs, or operational procedures change.

Useful documentation targets include:

- README
- docs/*
- API docs
- `.env.example`
- changelog
- runbooks
- comments near non-obvious code

Do not add obvious comments. Prefer comments that explain why, not what.

## 20. Final Response Requirements

After completing work, Codex must provide a concise final summary:

```md
## Summary
- What changed.

## Verification
- Commands run and results.
- Tests added or updated.
- Checks not run, with reason.

## Files Changed
- Key files and purpose.

## Risks / Follow-ups
- Remaining risks.
- Suggested follow-up work.
```

If no code was changed, Codex must clearly say so.

## 21. Prohibited Behavior

Codex must not:

- Make unrelated changes.
- Hide failed commands.
- Claim tests passed without running them.
- Claim behavior is verified without evidence.
- Remove tests to make checks pass.
- Ignore failing tests unrelated to the task without reporting them.
- Rewrite large areas without approval.
- Invent product requirements.
- Invent APIs, tools, commands, or files.
- Perform destructive operations without approval.
- Use real secrets or production data.
- Continue after discovering material ambiguity in high-risk areas.

## 22. Suggested Prompt Rewriting Template

When a human gives an abstract instruction, Codex should internally and explicitly convert it like this:

```md
Original human request:
> ...

Rewritten task for implementation:
> Implement [specific change] in [specific area] so that [observable outcome].
> Preserve [constraints].
> Do not change [non-goals].
> Verify by running [commands] and adding/updating [tests].
```

Example:

```md
Original:
> Make login better.

Rewritten:
> Investigate the current login flow and propose a plan before implementation. Identify existing files, validation behavior, error messages, auth/session handling, and tests. Do not change authentication semantics without approval. If safe, improve user-facing validation and error handling with minimal diffs, add regression tests, and verify with lint, typecheck, and relevant tests.
```

## 23. Skills Usage Policy

Codex should use project skills when available.

Recommended skills for this repository:

- `instruction-rewriter`
- `subagent-orchestrator`
- `implementation-plan`
- `security-review`
- `pr-review`
- `bug-fix`
- `test-generation`
- `release-check`

If a relevant skill exists, Codex must use it before ad hoc execution.

## 24. Repository-Specific Overrides

Add project-specific rules below.

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

## Architecture
-

## Important Files
-

## Environment Variables
- See `.env.example`.
- Never use or commit real secrets.

## Product Constraints
-

## Security Constraints
-

## Deployment Notes
-
```
