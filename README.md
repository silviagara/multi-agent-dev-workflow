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
