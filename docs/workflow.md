# Workflow

This document describes the end-to-end flow for a supervised multi-agent software development loop.

## High-level flow

```text
request
  → task interpretation
  → issue creation
  → summary for human approval
  → go / skip
  → branch creation
  → coding agent execution
  → senior review
  → human validation
  → push / PR / merge
```

## 1. Trigger enters the system

A request arrives in natural language.

Examples:
- new feature
- fix bug
- improve output
- status request

The orchestration agent should not jump straight into coding.
It should first translate the request into a task that can be tracked.

## 2. Task interpretation

The CTO agent turns the raw request into:
- a technical task
- a clear problem statement
- an implementation direction
- a complexity estimate
- a risk estimate

This is where ambiguity gets reduced.

## 3. Issue creation

Create a structured issue with:
- title
- context
- acceptance criteria
- non-goals
- complexity
- risks

This matters because it anchors the work outside the chat thread.

## 4. PM-friendly summary

Before implementation starts, the human should get a short summary:
- what this is
- why it matters
- how it will be approached
- what the risk is

The goal is not to ask for technical review.
The goal is to get a **go / skip / correction**.

## 5. Approval gate

If the human says:
- **go** → continue
- **skip** → stop
- **correct** → revise the plan

This prevents the coding agent from running on bad assumptions.

## 6. Branch creation

The CTO agent creates a branch tied to the task.

Example naming:
- `feat/PROJ-12-step0-robustness`
- `fix/PROJ-7-jira-loader`

## 7. Coding agent execution

The developer agent does the implementation work.

It should:
- edit files
- run commands
- test what it changes
- stay within scope

It should **not** silently redefine the task.

## 8. Senior review pass

The CTO agent reviews the result before the human sees it.

Review should check:
- scope fit
- code quality
- obvious regressions
- overengineering
- mismatch between request and implementation

If the work is weak:
- send it back to the coding agent
- keep the loop tight
- escalate only after repeated failure

## 9. Human validation

The human should validate the outcome in a role-appropriate way.

Usually this means:
- does the behavior look right?
- is the product logic right?
- does the output make sense?

Not:
- line-by-line code review unless they want that

## 10. Push / PR / merge

Once validated:
- commit
- push
- open PR
- merge if appropriate
- close issue

The exact level of human involvement here can vary.
Some workflows require a final approval before push; others don’t.

## 11. When the agent should ping the human

Good moments to ping:
- after the task summary
- after implementation review
- when a blocker appears
- when the coding agent fails repeatedly
- when a business decision is needed

Bad moments to ping:
- every tiny step
- status spam
- internal uncertainty that can be resolved locally

## 12. Failure handling

### If the coding agent fails once
Retry with tighter instructions.

### If it fails multiple times
Escalate with a concise explanation.

### If the request is underspecified
Ask for clarification early, not after implementation drift.

### If the task is growing out of scope
Stop and split it.

## 13. What makes this workflow work

- role separation
- issue-first execution
- human approval at the right points
- review before push
- silence between touchpoints

The point is not to imitate a big company.
The point is to make AI-assisted development feel structured, fast, and sane.
