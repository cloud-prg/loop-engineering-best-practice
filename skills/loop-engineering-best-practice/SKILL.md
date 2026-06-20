---
name: loop-engineering-best-practice
description: Design, review, and harden self-running agent loops. Use when building recurring or autonomous agent workflows with timers, Chrome/browser checks, skills, plugins, MCP connectors, worktrees, subagents, memory/state files, evaluator agents, budget limits, or human review gates.
---

# Loop Engineering Best Practice

Use this skill to turn a one-off agent prompt into a controlled loop: a system that discovers work, hands it to agents, verifies the result, persists state, and schedules the next run without relying on a human to keep prompting it.

The core principle: do not merely automate prompting. Replace the human-in-the-loop prompt cadence with an engineered loop that has boundaries, memory, an independent evaluator, cost ceilings, and a clear human review point.

## First Response

When asked to design or implement a loop:

1. Identify the loop's job in one sentence.
2. State the file(s), services, browser surfaces, and external systems the loop may read or write.
3. State the loop plan in 3-6 bullets before editing files or wiring automations.
4. Start small: build a single-line loop before adding parallelism, plugins, or cloud scheduling.
5. Add the checker before increasing autonomy.

If the user asks for implementation, implement the smallest reviewable loop and run the fastest relevant verification.

## The Stack

Keep these layers separate:

- Prompt engineering: what to tell the model in one turn.
- Context engineering: what belongs in the current context window.
- Harness engineering: what one agent run can do, with what tools and permissions.
- Loop engineering: what repeatedly wakes, feeds, checks, and advances agent runs.

Do not solve a loop problem by pasting a giant prompt into a timer. Timers should invoke a named skill or script whose logic can be maintained.

## The Five Moves

Every loop pass must have these moves:

1. Discovery: find what is worth doing now. Examples: failing CI, new issues, recent commits, inbox items, browser errors, production alerts.
2. Handoff: isolate and assign the work. Prefer one task per worktree, branch, ticket, or background thread.
3. Verification: run objective checks and use an evaluator that did not produce the change.
4. Persistence: write state outside the chat context. Examples: `loop-state.md`, issue comments, PRs, Linear/Jira tickets, logs.
5. Scheduling: decide when and why the next pass runs. Use a fixed interval only when it matches the work; otherwise use event-driven wakes plus a fallback heartbeat.

If one of these moves is missing, name the missing move and the risk before proceeding.

## The Six Parts

Use these parts deliberately:

- Automation: timer, watcher, GitHub Actions schedule, desktop automation, or cloud routine that wakes the loop.
- Worktrees: isolate parallel agents so concurrent edits do not collide.
- Skills: store reusable project knowledge in `SKILL.md`; trigger `$skill-name`, not a wall of instructions.
- Plugins and MCP connectors: let the loop reach external systems such as GitHub, Slack, Linear, databases, staging APIs, or browser automation.
- Subagents: split maker and checker. One agent generates; a second agent with different instructions evaluates.
- Memory: persist state on disk or in a tracker. Context is temporary; memory must survive the next run.

Add only the parts the current loop needs. More parts increase blast radius.

## Maker-Checker Rule

Never let the agent that produced a change be the only judge of that change.

For code loops:

1. Maker drafts the change in an isolated workspace.
2. Checker starts fresh, reads the diff and requirements, and assumes the change is wrong until proven otherwise.
3. Checker runs real evidence: tests, lint, typecheck, build, browser actions, screenshots, logs, API probes, or data queries.
4. Human review remains the gate for risky changes, production writes, security/auth changes, migrations, billing, or irreversible operations.

Prefer a checker that can act, not only read. For frontend/browser loops, have the checker open Chrome or Playwright, click through the flow, inspect DOM/network/errors, and capture evidence.

## Scheduling Choices

Choose scheduling by what the loop must observe:

- Local `/loop` or desktop timer: good for local dev servers, local files, short intervals, and active sessions. It stops when the machine/session is unavailable.
- Event watcher: good for logs, file changes, CI completion, PR events, or queue changes.
- GitHub Actions schedule or cloud routine: good when the machine may be off and the loop can run from a clean clone.
- Codex/agent automations: good for recurring agent work that should land in a triage inbox or background worktree.

Use a unique sentinel, state file, or run id so ticks do not overlap or double-run.

## Guardrails

Before increasing autonomy, set hard limits:

- Scope: allowed files, repos, branches, services, and operations.
- Budget: max tokens/cost per run and per day.
- Retry: max retries and backoff.
- Concurrency: max active agents/worktrees.
- Time: max wall-clock duration per pass.
- Write policy: when to open a PR, when to stop, and when to ask for human review.
- Secrets: never paste credentials into prompts, files, logs, PRs, or issue comments; require environment variables for secrets.

If the loop cannot prove it stayed inside these limits, treat the pass as failed.

## Chrome And Browser Checks

Use Chrome/browser automation when the loop changes or monitors user-facing behavior:

1. Start or locate the app/dev server.
2. Open the target route in Chrome or Playwright.
3. Exercise the critical path: click, type, submit, navigate, resize, and refresh as relevant.
4. Inspect console errors, network failures, DOM state, screenshots, and visible regressions.
5. Persist the evidence path or summary into the loop state.

Do not accept "looks correct from code" for UI loops when a browser check is possible.

## State File Pattern

Use a small repo-tracked or workspace-local state file for simple loops:

```markdown
# Loop State

## Objective
One sentence.

## Last Run
- run_id:
- started_at:
- completed_at:
- trigger:
- status:
- budget_used:

## Queue
- [ ] task id, source, priority, owner/worktree, current status

## Decisions
- date: decision, evidence, next action

## Escalations
- item, why automation stopped, what human must decide
```

Keep memory concise. Store large logs as artifacts and link to them.

## When To Read The Reference

Read `references/loop-checklists.md` when:

- designing a new loop from scratch,
- reviewing a loop before enabling recurring execution,
- deciding whether to add plugins/MCP, subagents, worktrees, or browser automation,
- diagnosing runaway loops, shallow self-review, token spikes, or stale context.

## Done Criteria

A loop design is ready only when it names:

- discovery source,
- handoff boundary,
- independent evaluator,
- persisted state,
- schedule/trigger,
- budget and retry ceilings,
- human review gate,
- stop/escalation condition.

If any item is unknown, deliver a bounded one-off workflow first and mark the missing loop contract explicitly.
