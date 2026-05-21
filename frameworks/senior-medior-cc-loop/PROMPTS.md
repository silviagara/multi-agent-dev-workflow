# Reusable Prompts

## A) Kickoff prompt (full loop)

Run Senior/Medior loop for ticket <ID> in repo <owner/repo>.

Constraints:
- Follow 8-gate workflow strictly.
- Do not code before plan approval.
- Do not push/PR before local build report approval.
- Prefer localhost QA.
- Use fallback deploy only if localhost is blocked, with explicit reason.
- Do not merge without explicit "merge now".

Output format each cycle:
1) What changed
2) Checks run
3) Risks
4) Decision needed from human

## B) Batch mode prompt (backlog burn)

Run Senior/Medior loop on top <N> backlog tickets.

Prioritization:
- Start with small, clear, low-risk tickets.
- Defer ambiguous/high-risk tickets unless requested.

For each ticket return:
- planned scope
- ETA class (S/M/L)
- dependency risks
- next human decision point

## C) Bot-fix loop prompt

Review PR <PR_NUMBER> bot/CI comments.
Classify into:
- blocker
- should-fix
- noise

Apply only blocker + high-value should-fix comments.
Max 2 loops unless blocker remains.
Return concise final merge recommendation.
