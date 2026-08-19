# Multi-Agent Dev Workflow

A practical guide to running software development with a small team of specialized AI agents instead of one all-purpose assistant.

## What this is

This repository documents a replicable operating model for building software with AI agents that have distinct roles.

The goal is not “full autonomy.” The goal is **structured autonomy**:
- one human making product decisions
- one orchestration agent keeping work aligned
- one coding agent doing implementation
- one repeatable workflow from request to merge

## Core model

### Human = CEO / PM
The human sets direction, defines priorities, approves tradeoffs, and validates outcomes.

### CTO agent
The orchestration agent:
- translates requests into technical tasks
- manages issues, branches, and next steps
- reviews implementation output
- protects focus and prevents overengineering

### Developer agent
The coding agent:
- writes code
- edits files
- runs commands
- iterates on implementation under constraints

## What problems this solves

A multi-agent workflow is useful when you want to reduce:
- context switching
- vague prompts
- planning fatigue
- implementation drift
- review overhead

It is especially useful for:
- indie builders
- product-minded developers
- founders building with AI leverage
- engineers who want AI help without losing control of the process

## Workflow at a glance

```text
request
  → CTO interprets task
  → issue created
  → PM-friendly summary
  → go / skip
  → branch created
  → coding agent runs
  → CTO review
  → human validation
  → push / PR / merge
```

## Repo guide

- `docs/setup.md` — install and configure the environment
- `docs/skill-file-guide.md` — write better skill files for agent behavior
- `docs/workflow.md` — end-to-end operating workflow
- `examples/skill-file-template.md` — reusable template for your own setup
- `examples/trigger-examples.md` — example triggers, responses, and interaction patterns

## Who this is for

This guide is for people who want AI agents to become part of a real development process — not just chat helpers.

If you want:
- sharper delegation
- repeatable flows
- clearer agent roles
- less prompt chaos

this repo is for you.

## What this is not

This is not:
- a no-code magic trick
- a promise of unsupervised software delivery
- a generic “AI productivity tips” page

It is a practical workflow for supervised, high-agency development with AI agents.

## Design principles

- **Role separation beats one giant prompt**
- **Workflows beat improvisation**
- **Structured touchpoints beat constant interruptions**
- **Human approval stays at key decision points**
- **Good agent behavior comes from clear operating constraints**

## What production teaches you

Designing a multi-agent workflow on paper is the easy part. A few principles — well
known in the human-in-the-loop and multi-agent literature — matter far more once the
system is actually doing work:

- **An agent's confidence is not evidence.** Models will report a task as done, a state as
  safe, or a file as written with complete conviction and be wrong; treat every consequential
  claim as a hypothesis to verify against a real artifact, not a fact.

- **Put the human at the decisions, not in the loop of every step.** Approval gates belong on
  the actions that are hard to undo — merges, deploys, anything touching production or
  credentials — and nowhere else, or the human becomes the bottleneck the system was meant to remove.

- **Make actions reversible before you make them autonomous.** An agent you can let run is one
  whose mistakes you can cheaply undo; branches, backups, and staged changes are what turn
  "risky" into "acceptable."

- **State that isn't written down doesn't exist to the next session.** Agents wake up blank, so
  the memory you persist — decisions, constraints, what went wrong last time — is the only thing
  separating an assistant that repeats mistakes from one that compounds knowledge.

- **Least privilege isn't just security, it's blast-radius control.** An agent that can only reach
  what its current task needs will fail small when it fails; broad standing access turns a single
  bad step into a wide mess.

- **Narrow, verifiable steps beat clever end-to-end runs.** A chain of small actions you can each
  inspect is easier to trust, debug, and recover than one impressive leap whose failure you can't localize.

- **The orchestrator's real job is scoping, not doing.** Most of the value in a coordinating agent
  comes from turning a vague goal into a bounded, well-specified task — get that wrong and no amount
  of downstream capability saves the run.
  
## What you need to replicate this

At minimum, you need:
- one active repository
- one issue tracker
- one orchestration agent with tool access
- one coding agent you can invoke reliably
- one human willing to enforce approval points

## Suggested starting point

If you want to replicate this model, start here:
1. read `docs/setup.md`
2. read `docs/workflow.md`
3. copy `examples/skill-file-template.md`
4. adapt the trigger language to your own workflow
5. keep the first version simple

## Why this matters

A lot of AI usage in software development is still prompt-by-prompt improvisation.

This guide is about something more useful:
**turning AI help into an operating system for shipping work.**
