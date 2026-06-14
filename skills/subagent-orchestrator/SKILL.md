# Skill: subagent-orchestrator

Use this skill after `instruction-rewriter` for any non-trivial task.

## Purpose

Delegate work to explicit subagents while keeping the main agent responsible for task framing, integration, review, and final decision-making.

The preferred implementation pattern is:

- Main agent rewrites the task.
- Main agent starts with Plan Mode.
- Main agent delegates investigation, implementation, and review to subagents.
- Subagents use the same model family with Low reasoning if the environment supports it.
- Main agent evaluates and integrates the result.

## When to Use

Use this skill for:

- Multi-file changes.
- Feature work.
- Bug fixes with unknown cause.
- Refactors.
- Test generation.
- Security-sensitive changes.
- API or database work.
- Any task requiring investigation before implementation.

## Delegation Template

```md
# Subagent Delegation

## Main Agent Responsibility
- Own the rewritten task.
- Decide scope.
- Protect constraints.
- Review all subagent outputs.
- Integrate final changes.
- Produce final response.

## Subagent A: Investigator
Model / reasoning:
- Same model family, Low reasoning if available.

Prompt:
"""
You are the Investigator subagent.

Use the rewritten task below.

Do not modify files.

Identify:
- Relevant files.
- Existing patterns.
- Existing tests.
- Constraints.
- Edge cases.
- Security or privacy risks.
- Recommended implementation path.

Return concise findings with file references.

Rewritten task:
[PASTE REWRITTEN TASK]
"""

Expected output:
- Findings.
- Relevant files.
- Existing conventions.
- Risks.
- Recommended approach.

## Subagent B: Implementer
Model / reasoning:
- Same model family, Low reasoning if available.

Prompt:
"""
You are the Implementer subagent.

Use the rewritten task and investigator findings below.

Implement the smallest safe change.
Follow existing project conventions.
Do not add dependencies.
Do not perform risky operations.
Add or update tests.
Do not make unrelated changes.

Rewritten task:
[PASTE REWRITTEN TASK]

Investigator findings:
[PASTE FINDINGS]
"""

Expected output:
- Files changed.
- Summary of implementation.
- Tests added or updated.
- Commands run.
- Known issues.

## Subagent C: Reviewer
Model / reasoning:
- Same model family, Low reasoning if available.

Prompt:
"""
You are the Reviewer subagent.

Review the diff against the rewritten task.

Focus on:
- Correctness.
- Security.
- Privacy.
- Authorization.
- Error handling.
- Test coverage.
- Type safety.
- Backward compatibility.
- Unrelated changes.

Return findings by severity:
- Blocking
- Important
- Minor
- No issue

Do not implement fixes unless explicitly asked.

Rewritten task:
[PASTE REWRITTEN TASK]

Diff or implementation summary:
[PASTE DIFF/SUMMARY]
"""

Expected output:
- Findings by severity.
- Required fixes.
- Suggested improvements.
```

## Rules

- Use the fewest subagents needed.
- Do not delegate high-level product judgment to subagents.
- Do not let subagents override explicit human constraints.
- If the environment does not support actual subagents, emulate this structure in separate labeled phases.
- If Low reasoning is not configurable, state that the environment does not expose that control and proceed with explicit subagent-style workstreams.
- Subagents must not request human approval directly; they must report approval needs to the main agent.
- The main agent must make the final call.

## Final Integration Checklist

Before final response, confirm:

- Rewritten task was followed.
- Scope did not expand.
- Required tests were added or updated.
- Verification commands were run or skipped with reason.
- Reviewer findings were resolved or reported.
- Approval gates were respected.
- No secrets or production data were exposed.
