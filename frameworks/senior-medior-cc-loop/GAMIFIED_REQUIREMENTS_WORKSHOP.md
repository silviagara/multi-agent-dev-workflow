# 🎮 Requirements Quest — 20-Min Co-op (Mission Commander + Claw + CC)

Use this before coding tasks so planning feels concrete (and fun), not abstract.

---

## Why this exists

You said requirements feel abstract. This quest turns them into a **short game**:
- You earn points for clarity
- I coach and translate into an execution brief
- CC gets cleaner instructions and writes better code

Result: faster delivery, less rework, safer merges.

---

## Players

- **Mission Commander** = Mission Commander (sets intent + final approval)
- **Claw** = Strategy NPC (turns intent into a clear brief)
- **CC** = Builder Bot (implements)

---

## Game loop (20 minutes total)

### Level 1 — Mission Card (5 min)
Fill these quickly (rough bullets, not perfect writing):

1. **Mission goal** (1 sentence)
2. **Why now** (business/user impact)
3. **In scope** (what we touch)
4. **Out of scope** (what we do NOT touch)

**Score:** +10 XP if all 4 are present

---

### Level 2 — Guardrails (5 min)
Add constraints and done criteria:

- Constraints:
  - i18n ES/EN parity
  - no env/secret risk
  - minimal reversible change
- Done criteria:
  - behavior works
  - no obvious regressions
  - manual dev validation steps listed

**Score:** +10 XP if constraints + done criteria are explicit

---

### Level 3 — Risk Radar (3 min)
List 2–3 things that could break.

Template:
- Risk:
- How to detect:
- Fallback/rollback:

**Score:** +10 XP if each risk has detection + fallback

---

### Level 4 — Orchestration Brief (5 min)
Prompt Claw with your Mission Card + Guardrails + Risk Radar:

> "Turn this into a CC execution brief: root cause hypothesis, minimal file-level plan,
> risk checks, validation checklist, and PR-ready summary. Keep it concise."

I return a brief that CC can execute.

**Score:** +15 XP if brief is implementation-ready without extra clarification

---

### Level 5 — Boss Check (2 min)
Before sending to CC, answer yes/no:
- Is scope clear?
- Is done definition testable?
- Are risky assumptions visible?

If yes, ship to CC.

**Score:** +5 XP if all yes

---

## XP tiers

- **0–20 XP:** Explorer (still vague)
- **21–35 XP:** Operator (good enough to code)
- **36–50 XP:** 5x Engineer (clear, safe, scalable)

Target: **36+ XP** before CC starts coding.

---

## Copy-paste Mission Card (quick form)

```md
# Mission Card

## Goal

## Why now

## In scope
- 

## Out of scope
- 

## Constraints
- ES/EN parity
- no secret/env risk
- minimal reversible changes

## Done means
- [ ] feature/bug behavior validated
- [ ] no obvious regressions
- [ ] manual dev test steps documented

## Top risks
- Risk 1:
  - Detect by:
  - Fallback:
- Risk 2:
  - Detect by:
  - Fallback:
```

---

## Daily usage (where this fits)

1. Mission Commander fills Mission Card (5 min)
2. Claw converts to execution brief (3–5 min)
3. CC plans/codes
4. Claw reviews compliance
5. Mission Commander final approval

This is the small step that makes the whole loop faster and safer.
