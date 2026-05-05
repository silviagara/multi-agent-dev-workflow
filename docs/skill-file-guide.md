# Skill File Guide

Skill files are where a lot of the real leverage lives.

A good skill file does not just describe an agent. It defines how that agent should operate inside a workflow.

## What a skill file should do

A good skill file should:
- define the agent’s role
- define the team model
- define the workflow
- define the touchpoints where a human must be involved
- define what “good behavior” looks like

If the file only sounds impressive, it is not doing its job.

## What to include

### 1. Role definition
Be explicit.

Bad:
- “You are a helpful AI assistant.”

Better:
- “You are the CTO agent. You plan work, manage backlog, review code, and decide what gets shown to the human.”

### 2. Team model
Say who is who.

Example:
- human = CEO / PM
- orchestration agent = CTO
- coding agent = developer

This matters because it prevents role confusion.

### 3. Stack and tools
List what the agent can assume.

Example:
- language
- repo location
- issue tracker
- coding agent CLI
- deployment assumptions

### 4. Main workflow
Spell out the sequence.

Example:
1. interpret request
2. create issue
3. show PM summary
4. wait for go / skip
5. create branch
6. launch coding agent
7. review diff
8. ask for validation
9. push / PR / merge

### 5. Required touchpoints
This is critical.

Define when the human must be pinged, and when the agent should stay quiet.

Without this, agents either:
- interrupt constantly
- or hide too much

### 6. Hard rules
Examples:
- never push to main without approval
- never delete files without confirmation
- never modify secrets
- do not overengineer

## What to avoid

### Vague values without workflow
“Be strategic, thoughtful, and helpful” is fluff if it is not tied to behavior.

### Too many edge-case rules
If the file becomes a policy graveyard, the agent will stop feeling coherent.

### Personal diary content
A skill file is an operating artifact, not a life log.

### Trying to solve everything in one file
Keep it specific to one workflow or operating model.

## What makes a skill file effective

A strong skill file is:
- operational
- opinionated
- readable
- specific enough to constrain behavior
- flexible enough to survive real work

## Good pattern

A useful pattern is:
- role
- team
- stack
- workflow
- touchpoints
- triggers
- hard rules
- active project context

## Review checklist

Before using a skill file, ask:
- does it define a real role?
- does it reduce ambiguity?
- does it specify when to involve the human?
- does it prevent obvious failure modes?
- does it make the workflow faster, not more ceremonial?

## Final advice

The best skill files read like operating manuals, not prompt poetry.

If someone else could copy your file, adapt a few details, and run a better AI workflow because of it, you wrote a good one.
