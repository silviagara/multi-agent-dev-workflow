# Senior/Medior CC Loop

A reusable workflow where:
- **Claw** acts as senior reviewer/orchestrator
- **Claude Code (CC)** acts as implementation agent
- **Human** is final approver at explicit gates

This flow is designed to clear backlog quickly while keeping risk controlled.

---

## What this flow optimizes for

- Fast ticket throughput
- Strong review discipline
- Human control on risky decisions
- Predictable quality gates

---

## Roles

- **Human**
  - Approves plan
  - Runs manual QA (localhost first)
  - Approves merge explicitly

- **Claw (orchestrator)**
  - Scopes tasks
  - Reviews CC output
  - Triages bot feedback
  - Decides blocker vs noise
  - Coordinates iterations

- **Claude Code (executor)**
  - Implements scoped changes
  - Runs available checks
  - Commits/pushes/opens PR when instructed
  - Applies follow-up fixes

---

## The 8 gates (must be followed in order)

1. **Intake gate**
   - Define scope, acceptance criteria, non-goals, risk level.

2. **Plan gate**
   - CC proposes plan.
   - Claw reviews/refines.
   - Human approval required before coding.

3. **Local build gate**
   - CC implements in feature branch.
   - CC runs lint/typecheck/tests available.
   - CC reports changed files + risks.
   - Human approval required before push/PR.

4. **Localhost QA gate**
   - Human runs manual localhost tests with checklist.

5. **Fallback gate (CD trick only if blocked)**
   - Only if localhost testing is impossible.
   - Must include explicit justification.
   - Use manual deploy fallback policy.

6. **PR/Bot gate**
   - CI + bots + code review feedback triaged into blocker/should-fix/noise.
   - Max 1–2 micro-fix loops unless blocker remains.

7. **Merge gate**
   - Human gives explicit: **"merge now"**.
   - No auto-merge without explicit approval.

8. **Aftercare gate**
   - Post-merge smoke test.
   - If fallback deploy was used, restore normal development deployment state.

---

## Daily operating rhythm

- Pull top backlog tickets
- Prioritize small and clear tickets first (throughput mode)
- Run loop ticket-by-ticket in short cycles
- Keep only decision points for human attention

See:
- `OPERATING_CONTRACT.md`
- `CHECKLISTS.md`
- `PROMPTS.md`
- `DAILY_RUNBOOK.md`

---

## Default policy summary

- Localhost testing is preferred.
- Manual deploy fallback is exception-only and justified.
- Human remains final quality and merge authority.
- Optimize for closure velocity, not uncontrolled automation.
