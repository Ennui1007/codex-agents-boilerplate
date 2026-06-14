# Greenfield Project Prompt

Use this prompt when starting a new app or service.

```md
Do not implement yet.

Use Plan Mode.

First produce a project creation plan for this app/service.

Product idea:
[DESCRIBE PRODUCT]

Please produce:

1. Rewritten project brief
2. Product scope
3. Non-goals
4. Architecture proposal
5. Dependency proposal
6. Directory structure proposal
7. Data model proposal, if needed
8. Auth strategy, if needed
9. Payment strategy, if needed
10. Testing strategy
11. Implementation phases
12. Approval gates
13. Token efficiency plan

Constraints:
- Do not scaffold the full app yet.
- Prefer a minimal vertical slice.
- Do not add production dependencies without approval.
- Do not introduce auth, database, payments, email, analytics, background jobs, or infrastructure without identifying the decision first.
- Keep each phase small and verifiable.
```
