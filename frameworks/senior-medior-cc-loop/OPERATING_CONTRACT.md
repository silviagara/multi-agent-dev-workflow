# Operating Contract (Claw ↔ CC ↔ Human)

Use this contract as the default command policy.

## Contract

1. Never code before plan approval.
2. Never push/PR before local build report approval.
3. Never use fallback deploy unless localhost is blocked.
4. Never merge without explicit human approval.
5. Keep changes scoped to ticket acceptance criteria.
6. Prefer smallest diff that solves the ticket.
7. Limit bot-fix loops to avoid infinite churn.

## Stop conditions

Stop and escalate to human immediately if:
- acceptance criteria are ambiguous
- auth/security-sensitive paths changed unexpectedly
- local checks fail and root cause unclear
- required secrets/env prevent reliable validation
- bot comments conflict with product intent

## Definition of done (per ticket)

- Acceptance criteria met
- Scope boundaries respected
- Required checks executed
- Manual QA executed (localhost preferred)
- PR feedback addressed (or documented as non-blocking)
- Human approved merge
