# Checklists

## 1) Intake checklist
- [ ] Ticket ID linked
- [ ] One-line problem statement
- [ ] Acceptance criteria listed
- [ ] Non-goals listed
- [ ] Risk label: low / medium / high

## 2) Plan approval checklist
- [ ] CC plan received
- [ ] Plan reviewed by Claw
- [ ] Edge cases identified
- [ ] Human approved plan

## 3) Local build checklist
- [ ] Feature branch created
- [ ] Implementation complete
- [ ] Lint run
- [ ] Typecheck run
- [ ] Tests run (if present)
- [ ] Changed files summarized
- [ ] Risk notes included
- [ ] Human approved push/PR

## 4) Manual localhost QA checklist
- [ ] Core happy path tested
- [ ] Critical error path tested
- [ ] Auth/session flow tested (if relevant)
- [ ] No obvious regressions

## 5) Fallback deploy checklist (exception-only)
- [ ] Localhost blocked confirmed
- [ ] Reason documented clearly
- [ ] Manual deploy triggered
- [ ] Dev-app/manual QA evidence captured
- [ ] Restore normal deployment flow after testing

## 6) PR/Bot checklist
- [ ] CI status green or justified
- [ ] Bot comments triaged: blocker / should-fix / noise
- [ ] Fix loop count <= 2 (unless blocker)
- [ ] Final risk summary shared

## 7) Merge checklist
- [ ] Human said: "merge now"
- [ ] Merge strategy matches repo policy
- [ ] Post-merge smoke test completed
