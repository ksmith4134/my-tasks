---
name: review-code
description: Review the local implementation of a numbered task for bugs and requirement gaps, and append evidence and findings to code-review.md without implementing fixes.
---

# Review task code

Use [my-tasks](../my-tasks/SKILL.md) to locate the task under `./tasks`. Use the task number supplied by the user or clearly identified in the conversation; ask only if it cannot be identified. Follow the [review handoff](../my-tasks/review-handoff.md) before, during, and after the review.

## Review

1. Read `AGENTS.md`, the task's `TASK.md`, and the project documents linked from `AGENTS.md` that are relevant to the changed code.
2. Read any existing `code-review.md` and recheck findings left open in earlier rounds.
3. Identify the implementation from the approved plan, implementation notes, and current local files. Follow relevant callers, service boundaries, and tests. Describe this scope honestly; do not claim to have reviewed a historical diff. If implementation is missing or scope is unclear, report the limitation.
4. Check correctness, task requirements, and the project constraints in `AGENTS.md`.
5. Trace each finding to concrete behavior and its consequence. Respect settled decisions; identify any decision that the code contradicts or that evidence calls into question. Consult authoritative documentation when needed for an API or library claim.
6. When useful, run the existing offline checks listed in `AGENTS.md`. Do not run application entry points, scripts that change external state, or live checks as part of this review. Leave reproduction requiring new tests to [test-plan](../test-plan/SKILL.md).
7. Append a round to the task's `code-review.md`. If checks ran, add a concise result and report link under `TASK.md`'s Validation section. Complete the handoff, then give the user a short summary and a link to the report.

Do not implement fixes, change existing tests, or create temporary tests. Preserve task requirements, status, approval, dates, and unrelated edits. Keep credentials and personal contact details out of findings and output.

## Report

Start a new report with `# Task NNNN code review`. Append rounds headed `## Round N: YYYY-MM-DD, commit <short hash> (<branch>)`, naming the commit and branch the round reviewed; preserve earlier rounds.

Include:

- **Reviewed:** local files and behavior inspected, checks run and their results, and scope limitations.
- **Rechecked:** earlier open finding numbers marked fixed, still open, no longer applicable, or not rechecked, with reasons where needed. Omit in round 1.
- **Summary:** whether the implementation meets the task and what remains unresolved. A review does not mark the task done.
- **Findings:** most severe first. Use P1 for must fix, P2 for should fix, and P3 for optional improvements. Continue finding numbers across rounds. Say explicitly when there are no findings.
- **Open questions:** owner decisions the implementation needs, if any.

For each finding, give **Relevant code** (files and lines), **Problem** (behavior, impact, and likelihood), **Evidence boundary** (what was traced or tested and what remains unconfirmed), and **Suggested fix**. Link authoritative documentation when the finding depends on it.
