---
name: critique-plan
description: Review a numbered task's local TASK.md plan for gaps, errors, and project fit, and append findings to critique-plan.md without implementing fixes.
---

# Critique a task plan

Use [my-tasks](../my-tasks/SKILL.md) to locate the task under `./tasks`. Use the task number supplied by the user or clearly identified in the conversation; ask only if it cannot be identified. Follow the [review handoff](../my-tasks/review-handoff.md) before, during, and after the review.

## Review

1. Read `AGENTS.md`, the task's `TASK.md`, and the project documents linked from `AGENTS.md` that are relevant to the plan.
2. Read any existing `critique-plan.md` and recheck findings left open in earlier rounds.
3. Check the plan against the task's requirements, prerequisites, completion checks, and current code. Look for unresolved business questions, live actions that `AGENTS.md` restricts, missing failure handling, and gaps in validation. If there is no concrete plan yet, report that limitation.
4. Respect settled decisions recorded in the task, `AGENTS.md`, or project documents. If the plan contradicts one or evidence shows it no longer holds, identify the decision and explain why.
5. Append a review round to the task's `critique-plan.md`, then complete the handoff. Give the user a short summary and a link to the report.

Review only. Do not implement fixes, edit `TASK.md`, run code or checks, or change task status or approval. Follow the project constraints in `AGENTS.md`. Keep credentials and personal contact details out of findings.

## Report

Start a new report with `# Task NNNN plan critique`. Append rounds headed `## Round N: YYYY-MM-DD, commit <short hash> (<branch>)`, naming the commit and branch the round reviewed; preserve earlier rounds.

Include:

- **Scope:** the local plan and supporting files reviewed, with any limitations.
- **Rechecked:** earlier open finding numbers marked fixed, still open, no longer applicable, or not rechecked, with reasons where needed. Omit in round 1.
- **Summary:** whether the plan is ready for owner approval and what remains unresolved. A critique does not grant approval.
- **Findings:** most severe first. Use P1 for must fix before implementation, P2 for should fix, and P3 for optional improvements. Continue finding numbers across rounds. Say explicitly when there are no findings.
- **Open questions:** owner decisions the plan needs, if any.

For each finding, give **Where** (section or file and line), **Problem** (evidence and consequence), and **Suggested fix** (a concrete plan change). Link authoritative documentation when a finding depends on it. Distinguish confirmed problems from questions or assumptions.
