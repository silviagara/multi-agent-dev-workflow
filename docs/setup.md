# Setup

This guide assumes you want a practical local setup for running a multi-agent development workflow.

## 1. Prerequisites

You need:
- a machine where you can run local commands
- Git and GitHub access
- a coding agent you can invoke from the CLI
- an orchestration layer that can manage message-based workflow and tool access

In the example workflow documented in this repo, OpenClaw is the orchestration layer, but the operating model can be adapted to other agent runtimes if they support tool use, role separation, and repeatable workflows.

## 2. Install OpenClaw

Install OpenClaw using the method documented for your environment.

Once installed, verify the CLI is available:

```bash
openclaw help
```

## 3. Check the gateway

OpenClaw relies on the gateway daemon for routing and runtime services.

Useful commands:

```bash
openclaw gateway status
openclaw gateway start
openclaw gateway restart
```

If something looks broken, start with:

```bash
openclaw status
```

## 4. Verify your runtime assumptions

Before building workflows, make sure you know:
- where your main workspace lives
- which repos you want agents to operate on
- which coding agent / model runtime is available
- whether messaging is local-only or connected to Telegram / Discord / another surface

## 5. Sanity-check your environment

You want to verify four things early:

### CLI works
```bash
openclaw help
```

### Gateway is alive
```bash
openclaw gateway status
```

### GitHub auth works
```bash
gh auth status
```

### Coding agent CLI works
Example:
```bash
claude --help
```

## 6. Choose your operating surface

You can run this workflow:
- locally in terminal only
- through a messaging surface
- through a persistent assistant session tied to your workspace

For most people, the right starting point is:
- one main assistant session
- one workspace
- one coding agent
- one GitHub account

Do not add complexity on day one.

## 7. Recommended first setup

Start with:
- one active repo
- one human decision-maker
- one orchestration agent
- one coding agent
- one issue tracker (usually GitHub Issues)

That is enough to validate whether the workflow helps or just creates ceremony.

## 8. Common setup mistakes

### Mistake 1: too many repos
If agents can touch everything, they become harder to supervise.

### Mistake 2: no skill file / no operating rules
Without constraints, the system becomes prompt soup.

### Mistake 3: trying full autonomy too early
You want supervised autonomy, not chaos with confidence.

### Mistake 4: mixing strategy, coding, and approval into one role
Role separation is the point.

## 9. What “good enough” setup looks like

You are ready to move on when:
- OpenClaw runs reliably
- your coding agent can be invoked from CLI
- GitHub operations work
- you know where your workspace and active repo live
- you can create and review tasks without friction

Then move to `docs/workflow.md`.
