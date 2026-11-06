# Review handoff

Shared steps for [critique-plan](../critique-plan/SKILL.md), [review-code](../review-code/SKILL.md), and [test-plan](../test-plan/SKILL.md). They let the agent that requested a review continue without the user relaying results.

## Before reviewing

If the request names a branch or pull/merge request, switch to that branch and pull the latest commits before reading anything.

## While reviewing

If the request names the task, proceed without asking questions. Record anything that needs an owner decision as an open question in the report and finish the round. Ask only when the task cannot be identified.

## After appending the round

1. Commit only the files this round changed: the report, plus `TASK.md` if you added a Validation entry. Leave other working-tree changes alone. Use a message such as `Task 0004: code review round 2`.
2. Push the branch.
3. If the branch has an open pull/merge request, comment on it with a two-to-four-line summary and the verdict line. Use the available CLI, such as `gh pr comment` or `glab mr note`.
4. If a commit, push, or comment fails, say so in your final message. Do not force-push or rewrite history to work around it.
5. End your final message with the verdict line.

## Verdict line

One line, exactly this shape:

```text
VERDICT stage=<critique-plan|code-review|test-plan> round=<N> result=<findings|clean|blocked> p1=<n> p2=<n> p3=<n> report=<repo-relative path>
```

- `round`: the round number just appended.
- `p1`, `p2`, `p3`: findings open after this round, new and still-open combined.
- `result`:
  - `findings`: at least one finding is open.
  - `clean`: no findings are open.
  - `blocked`: the review could not be completed, or progress needs an owner decision first. List the reasons in the report and your final message.
