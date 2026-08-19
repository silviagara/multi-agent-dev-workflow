# Trigger & Interaction Examples

A set of example interactions for the multi-agent workflow. These are illustrative patterns —
adapt the wording to your own setup. They show the *shape* of good triggers and responses,
not a fixed script.

---

## 1. Starting a task (a good trigger)

A good trigger gives intent and constraints, not implementation.

**Human:**
> Add a way for users to export their data as CSV. Keep it simple — one endpoint, no UI changes yet.

**Orchestrator agent:**
> Task understood. Scoping:
> - one endpoint, returns CSV
> - no frontend work this pass
> - out of scope: async/large exports, formatting options
> Definition of done: endpoint returns a valid CSV for a normal request, with a test.
> Creating the issue and a branch. Proceeding?

**Why it works:** the human gave *intent + a boundary* ("keep it simple"), and the orchestrator
turned it into a bounded task with an explicit out-of-scope list before touching anything.

---

## 2. A vague trigger (and how the orchestrator should handle it)

Don't let a vague request become a vague task. Narrow it first.

**Human:**
> The dashboard feels slow, do something about it.

**Orchestrator agent:**
> That's a direction, not yet a task. Before I hand anything off, one decision from you:
> is the priority (a) initial load time, (b) interaction lag after load, or (c) a specific slow view?
> I can investigate and propose, but a single focus makes the fix verifiable instead of open-ended.

**Why it works:** the agent refuses to convert ambiguity into busywork. It surfaces the choice to
the human rather than guessing — the human stays the decision-maker.

---

## 3. The orchestrator reviewing the coding agent's output

Review is where overengineering and drift get caught.

**Coding agent:**
> Implemented the export. Also refactored the serializer layer and added a caching wrapper
> while I was in there.

**Orchestrator agent:**
> The export itself looks correct and has a test — good. But the serializer refactor and the
> caching wrapper are out of scope for this task and add review surface and risk.
> Recommend: keep the export, revert the other two, and open a separate issue for the refactor
> if it's worth doing. Flagging for human decision.

**Why it works:** the orchestrator's job is protecting focus. Unrequested extra work — even
"helpful" work — is drift, and it gets named and separated, not merged silently.

---

## 4. A run that goes wrong (and how it recovers)

Recovery matters more than never failing.

**Coding agent:**
> Tests pass locally. Done — ready to merge.

**Orchestrator agent:**
> Before merge, verifying against the actual CI run, not your local report.
> CI shows the new test passing but two existing tests now failing on the same file.
> So this is not ready. Options: fix the regression this pass, or revert and re-scope.
> Not merging until the check is actually green. Flagging for human.

**Why it works:** the agent's "done" is treated as a claim to verify, not a fact. The safety
comes from checking a real artifact (CI) before an irreversible action (merge).

---

## 5. Approval at the right gate

Approvals belong on the expensive and irreversible, and nowhere else.

**Orchestrator agent:**
> Implementation is reviewed and CI is green. This is a merge to the main branch, so it's your call.
> Summary of what ships: one CSV export endpoint + test. No other changes.
> Approve merge? (Everything up to here ran without needing you — this is the one gate that does.)

**Why it works:** the human wasn't interrupted during scoping, implementation, or review — only
at the one step that's hard to undo. The gate is placed by consequence, not by habit.

---

## 6. Scoping something *out* (an underrated move)

Saying "not now" is part of keeping the system fast.

**Human:**
> While you're at it, can it also support Excel, PDF, and scheduled email exports?

**Orchestrator agent:**
> Those are three separate features, each bigger than the current task. If I fold them in,
> this stops being a small verifiable change and becomes a project.
> Recommendation: ship the CSV endpoint now, open a tracked item for the export-formats
> expansion, and decide priority on that separately. Keeping this pass small on purpose.

**Why it works:** the orchestrator protects the "small and verifiable" property of the task
instead of accepting scope creep. Deferred work is captured, not lost — and not smuggled into
an unrelated change.
