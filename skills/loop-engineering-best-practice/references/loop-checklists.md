# Loop Engineering Checklists

Use this reference for detailed design review. Keep the active `SKILL.md` lean and load this file only when deeper checks are needed.

## Loop Design Canvas

Answer these before enabling recurring execution:

| Area | Question | Good answer |
| --- | --- | --- |
| Objective | What repeated job does this loop own? | One durable sentence, not a vague "keep improving" goal |
| Discovery | What does it read to find work? | CI, issues, commits, logs, inbox, alerts, browser state |
| Handoff | How is work isolated? | One task per branch/worktree/thread/ticket |
| Tools | Which tools can it use? | Explicit list with read/write permissions |
| Skills | Which named skill owns the procedure? | `$skill-name`, not a pasted prompt wall |
| Connectors | Which external systems are needed? | GitHub, Linear, Slack, DB, staging, browser, etc. |
| Checker | Who can say no? | Fresh evaluator with objective checks |
| Memory | Where does state persist? | Markdown, tracker, PR, DB row, artifact log |
| Schedule | Why does the next run happen? | Time-based, event-based, or fallback heartbeat |
| Budget | What caps spend and retries? | Per-run/per-day token, time, retry, and concurrency limits |
| Human gate | Where must a human review? | Merge, deploy, security, data writes, irreversible actions |

## Start-Small Ladder

1. One-off run: perform the workflow once manually and write the state file.
2. Fixed local timer: repeat discovery and reporting only.
3. Add checker: require independent verification before any write.
4. Add write action: open PR/ticket/comment, but do not auto-merge.
5. Add worktrees: allow parallel isolated tasks.
6. Add connectors: pull from external systems after read/write scope is explicit.
7. Add cloud schedule: only after the loop works from a clean clone or documented environment.

Do not skip from step 1 to autonomous write loops.

## Evaluator Prompt Shape

Use this shape for checker agents:

```text
You are the evaluator, not the implementer.
Assume the change is broken until evidence proves otherwise.
Read the requirement, diff, and state file.
Run the fastest objective checks first.
For UI behavior, use Chrome/Playwright and capture evidence.
Return: pass/fail, evidence, residual risk, and the smallest required fix.
Do not approve based only on the maker's explanation.
```

## Browser Evidence Checklist

For UI loops, collect at least the relevant evidence:

- route opened successfully,
- critical controls visible and usable,
- core flow completed,
- console has no relevant errors,
- network has no unexpected failed requests,
- screenshot or DOM assertion captured,
- mobile/desktop layout checked when responsive behavior matters.

## Anti-Patterns

- Timer plus giant prompt: hard to maintain and easy to forget.
- Self-grading maker: the same context that made the decision approves it.
- Infinite helpfulness: no budget, retry, or wall-clock limits.
- No durable memory: every run starts as if nothing happened.
- Hidden writes: loop modifies external systems without an explicit write policy.
- Local-only assumptions in cloud: cloud job cannot see local servers, files, cookies, or uncommitted changes.
- Context hoarding: storing everything in prompt context instead of concise disk state plus linked artifacts.
- No human gate: risky changes go straight from agent to production.

## Cost Controls

Set these numbers before the first recurring run:

- maximum runs per day,
- maximum concurrent agents,
- maximum retries per task,
- maximum wall-clock minutes per pass,
- maximum token/cost budget per pass and per day,
- stop condition for repeated identical failures,
- escalation destination when the loop stops.

## Failure Handling

When a pass fails:

1. Persist the failure with run id, trigger, error, and evidence.
2. Stop retrying after the configured ceiling.
3. Avoid editing unrelated files while recovering.
4. Escalate if the loop needs secrets, production access, unclear business judgment, or destructive action.
5. Keep the next run from reprocessing the same failed item unless the state file says it is safe.

## Review Questions

Ask these during code review:

- Can this loop explain why it woke up?
- Can it prove what it changed?
- Can a fresh checker reproduce the evidence?
- Can it stop itself before spending too much?
- Can a human understand the state after a week away?
- Can it run from a clean clone if scheduled in cloud?
- What is the worst thing it can do if every model response is overconfident?

If the answer to any question is unclear, reduce autonomy before shipping.
