---
name: run-task
description: Claude only. Drive a numbered task through the Claude/Codex pipeline (plan, Codex plan critique, owner plan approval, implementation, Codex code review, fixes, Codex test run, fixes, owner final approval), handing review stages to Codex through pull/merge request comments, or automatically when T3 Code is available. Use when the user runs /run-task or asks to run the task pipeline.
---

# Run the task pipeline

Claude coordinates and does the planning and implementation; Codex runs the three reviews. Follow [my-tasks](../my-tasks/SKILL.md) for every task rule, including plan approval. This skill adds the order of stages and how each Codex handoff happens.

Codex should not run this skill.

## Stages

| # | Stage | Who | Next |
| --- | --- | --- | --- |
| 1 | Create or select the task, interview the user, and write the Plan in `TASK.md` | Claude | Hand off `critique-plan` |
| 2 | `$critique-plan` | Codex | Stage 3 |
| 3 | Judge the critique and revise the Plan | Claude | **Stop for plan approval** |
| 4 | Implement and write `test-plan.md` | Claude | Hand off `review-code` |
| 5 | `$review-code` | Codex | `findings`: stage 6. `clean`: hand off `test-plan` |
| 6 | Fix confirmed defects | Claude | Hand off `test-plan` |
| 7 | `$test-plan` | Codex | `findings`: stage 8. `clean`: stage 9 |
| 8 | Fix test failures | Claude | Hand off `test-plan` again for verification |
| 9 | Report results | Claude | **Stop for final approval** |

At any stage, a `blocked` verdict stops the pipeline. Show the user the open questions and wait.

### Stage notes

- **Stage 1:** Use my-tasks' Create a task and Plan before implementation sections. If you are on the default branch, create a task branch first. Commit, push, and open a pull/merge request for the task branch if none exists; running this pipeline authorizes that. Record its number in `git_pr`.
- **Stage 3:** Address findings as my-tasks' Review reports section describes. Then always stop: present the revised Plan, the findings you declined and why, and any open questions. Wait for explicit approval and record `plan_approved`. Do this even when the critique is `clean`.
- **Stage 4:** Follow my-tasks' Work a task section, including `test-plan.md`. Leave the task `in-progress`; it is marked `done` at stage 9.
- **Stages 6 and 8:** Address findings as my-tasks' Review reports section describes and record the finding numbers addressed in `TASK.md`.
- **Stage 9:** Summarize what changed, the validation results, declined findings, and anything still needing owner confirmation or authorization. Wait for the user's final approval, then mark the task `done` as my-tasks describes, commit, and push. Merging stays with the user.

### Rounds

Critique and code review each run once; Claude's fixes are not re-reviewed automatically. Test verification repeats at most twice after the first test run. If findings remain after the second verification, stop and bring them to the user instead of continuing.

## Handoff to Codex

Before every handoff, commit and push your work so the branch and pull/merge request show it.

Both routes give Codex the same brief, filled in:

> Run $<skill> for task <N> on branch <branch> (pull/merge request <url>). Do not ask questions. Follow `.agents/skills/my-tasks/review-handoff.md`: commit and push your report, comment on the pull/merge request, and end your final message with the verdict line.

For a verification run, add: "This is a verification re-test after fixes for findings <numbers>."

Use the T3 Code route when T3 Code's `delegate_task` tool is available. Otherwise use the pull/merge request route.

### Pull/merge request route (default)

Use this route in any environment without `delegate_task`, such as Claude Code on its own, locally or in the cloud.

1. Comment on the pull/merge request that the task is ready for Codex, with the brief in the comment so the user can paste it into Codex. If you cannot comment, put the brief in your final message instead.
2. End your turn. Tell the user which Codex stage to run and where the brief is.
3. When the user returns, for example with `/run-task <N>` or "continue", pull the latest commits and read Codex's result.

### T3 Code route (automatic, optional)

Use this route only when T3 Code's `delegate_task` tool is available.

1. Call `orchestrator_capabilities` once per thread before the first handoff. Stop and tell the user if the Codex provider is unavailable; do not substitute another provider.
2. Call `delegate_task` with `target.providerInstanceId` set to the Codex provider, `isolation: "shared"`, and `mode: "async"`. Use the provider's default model unless the user named one. Title it like `Task 0004: code review`. Give Codex the brief.
3. End your turn. T3 wakes you when the child finishes. Do not poll it, and do not watch the pull/merge request, which would wake you a second time for Codex's comment.

Codex works in the same checkout, so do not edit files while its task runs.

## Reading the result

Read the verdict line from Codex's final message (T3 Code route) or its pull/merge request comment (pull/merge request route), then follow the Stages table. If there is no verdict line, or you cannot read the comments, read the latest round of the named report. If the outcome is still unclear, stop and ask the user.

## Resuming

When `/run-task` is invoked for a task already underway, work out the stage from the task files, tell the user which stage you are resuming at, and continue:

| State | Resume at |
| --- | --- |
| No Plan in `TASK.md` | 1 |
| Plan, no `critique-plan.md` | Hand off `critique-plan` |
| `critique-plan.md` exists, `plan_approved` empty | 3 |
| Plan approved, no `test-plan.md` | 4 |
| `test-plan.md`, no `code-review.md` | Hand off `review-code` |
| `code-review.md`, no `test-report.md` | 6, or hand off `test-plan` if `TASK.md` already records the review findings as addressed |
| `test-report.md` exists | Follow the latest round's outcome: 8 if findings remain unaddressed, otherwise 9 |
