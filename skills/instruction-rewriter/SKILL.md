# Skill: instruction-rewriter

Use this skill whenever the human request is abstract, incomplete, ambiguous, broad, or outcome-oriented.

## Purpose

Convert a human’s rough request into an explicit, implementation-ready engineering task.

The rewritten task must be suitable for delegation to a lower-reasoning subagent and must reduce ambiguity before any code is changed.

## When to Use

Use this skill for:

- Feature requests.
- Bug reports.
- Refactors.
- Performance work.
- UI/UX changes.
- API changes.
- Test requests.
- Documentation requests.
- Security-sensitive work.
- Any prompt shorter than the complexity of the task requires.

Do not use this skill for trivial typo fixes unless the requested change is ambiguous.

## Output Format

Produce:

```md
# Rewritten Task

## Original Request
> ...

## Goal
-

## Context to Inspect
-

## Assumptions
| Assumption | Type | Why acceptable or risky |
|---|---|---|
| ... | Safe / Risky | ... |

## Constraints
-

## Non-goals
-

## Acceptance Criteria
-

## Verification Plan
-

## Risk Level
Low / Medium / High

## Human Approval Required
Yes / No

If yes:
- Approval item:
- Reason:
```

## Rules

- Do not implement during this skill.
- Do not invent facts about the codebase.
- If context is unknown, write “inspect repository” instead of guessing.
- Prefer safe assumptions over blocking questions.
- Mark risky assumptions clearly.
- Treat auth, payments, database, secrets, production data, and infrastructure as high-risk areas.
- The rewritten task must be specific enough that a subagent can execute it without interpreting product intent.

## Example

Human request:

```md
Make signup smoother.
```

Rewritten task:

```md
# Rewritten Task

## Original Request
> Make signup smoother.

## Goal
Improve the signup flow’s user-facing validation and error handling without changing authentication semantics.

## Context to Inspect
- Signup page or route.
- Auth handler.
- Validation schema.
- Existing tests.
- Existing UI components for forms and errors.

## Assumptions
| Assumption | Type | Why acceptable or risky |
|---|---|---|
| “Smoother” means clearer validation and fewer confusing errors, not changing auth policy. | Safe | Improves UX while preserving security. |
| Signup conversion metrics are not available locally. | Safe | The task can proceed without analytics changes. |

## Constraints
- Do not weaken password, email, rate limit, or anti-abuse behavior.
- Do not change session handling.
- Do not add dependencies.
- Do not log PII.

## Non-goals
- Redesigning the entire signup page.
- Changing authentication provider.
- Adding social login.
- Changing database schema.

## Acceptance Criteria
- User-facing validation messages are clearer.
- Existing auth behavior is preserved.
- Tests cover at least one invalid signup case and one successful case where applicable.

## Verification Plan
- Run relevant signup tests.
- Run lint and typecheck if available.
- Manually inspect changed UI strings.

## Risk Level
Medium

## Human Approval Required
No, unless auth semantics or schema changes are needed.
```
