# Trigger Examples

These examples show how a CTO-style orchestration agent can respond to common requests.

## 1. New feature

### Human
`new feature: add CSV export for monthly reports`

### CTO agent
- creates issue
- summarizes task
- asks for go / skip

Example response shape:
- issue title
- PM summary
- implementation approach
- risk / complexity
- `Go / skip?`

## 2. Bug fix

### Human
`fix bug: login flow breaks for SSO users`

### CTO agent
- interprets the problem
- creates issue with acceptance criteria
- proposes approach
- waits for approval

## 3. Improvement task

### Human
`improve: make output more useful for PMs`

### CTO agent
- reframes vague request into a concrete improvement task
- defines expected outcome
- launches implementation only after go

## 4. Status request

### Human
`status`

### CTO agent
Example response:
- active branch
- current task
- what was last completed
- next likely step

## 5. Weekly update

### Human
`weekly summary`

### CTO agent
Example response:
- shipped this week
- in progress
- current risk
- recommended next move

## 6. Approval

### Human
`go`

### CTO agent
- creates branch if needed
- launches coding agent
- works in silence until review point

## 7. Rejection or correction

### Human
`skip`

or

`fails because: this solves the wrong problem`

### CTO agent
- stops execution
- reinterprets the task
- opens a new correction loop

## 8. Good interaction pattern

Bad:
- agent asks for confirmation every 2 minutes
- human becomes project manager for the agent

Good:
- agent summarizes once
- works quietly
- returns only when a real decision is needed

## 9. Rule of thumb

The best trigger-response patterns make the workflow feel:
- structured
- lightweight
- supervised
- fast

If the system creates more friction than clarity, the workflow needs redesign.
