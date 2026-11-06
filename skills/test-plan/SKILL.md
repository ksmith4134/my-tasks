---
name: test-plan
description: Execute the validation plan for a numbered task using offline tests and authorized live checks, and record results in test-report.md and TASK.md without implementing fixes.
---

# Test a task

Use [my-tasks](../my-tasks/SKILL.md) to locate the task under `./tasks`. Use the task number supplied by the user or clearly identified in the conversation; ask only if it cannot be identified. Follow the [review handoff](../my-tasks/review-handoff.md) before, during, and after testing.

## Test

1. Read `AGENTS.md`, the task's `TASK.md`, and the project documents linked from `AGENTS.md` that are relevant to the task, including its development and testing guides.
2. Read any existing `test-report.md` and recheck findings left open in earlier rounds.
3. Take the test plan from `TASK.md`'s approved Plan and acceptance criteria, with the task's `test-plan.md` as the detailed checklist. `test-plan.md` does not override `TASK.md` or grant permission for live actions. If the plan or implementation is incomplete, report what can and cannot be tested.
4. Run the offline checks listed in `AGENTS.md`, using the isolated or mocked services and synthetic data the project's testing guide describes.
5. Test meaningful gaps and failure paths within the task's scope. Temporary offline tests are allowed in a new task-local directory. Remove only temporary files you created for this evaluation; preserve existing implementation, tests, and unrelated files. Consult authoritative documentation when needed to assess service behavior.
6. Where the task changes runtime behavior and development services are available, verify it in the running app as `AGENTS.md` and the development guide describe.
7. Keep live checks separate. Run a check that uses live services or changes external state only when `AGENTS.md` or the project documents give standing authorization for it, or the user explicitly authorizes that check. A general request to test a task or approval of its plan does not authorize it. Record unauthorized checks as not run, needing authorization.
8. Append a round to `test-report.md` and add a concise validation result and report link under `TASK.md`'s Validation section. Complete the handoff, then give the user a short summary, the report link, and any checks still needing authorization or owner confirmation.

Evaluate without implementing fixes or changing existing application code or tests. Preserve task requirements, status, approval, dates, and completion checkboxes. Follow the project constraints in `AGENTS.md`. Keep credentials and personal contact details out of reports and output. Do not retry or undo live actions to repair a failed check.

## Report

Start a new report with `# Task NNNN test report`. Append rounds headed `## Round N: YYYY-MM-DD`; preserve earlier rounds.

Include:

- **Scope:** local files and behavior tested, environment, mocked services, and the scope of any live check, identifying its authorization.
- **Rechecked:** earlier open finding numbers marked fixed, still open, no longer applicable, or not rechecked, with reasons where needed. Omit in round 1.
- **Results:** checks and commands, expected and actual outcomes, and passed, failed, or not-run status. Explain unavailable checks and pending owner acceptance without claiming success.
- **Findings:** most severe first. Use P1 for must fix, P2 for should fix, and P3 for optional improvements. Continue finding numbers across rounds. Say explicitly when there are no findings.
- **Test plan gaps:** missing cases, whether they were tested this round, and what should be added to the task's validation plan.
- **Open questions:** owner decisions or authorizations needed, if any.

For each finding, give **Relevant code** (files and lines), **Problem** (impact and likelihood), **Reproduction** (steps with synthetic inputs), **Evidence boundary** (what was reproduced and what remains unconfirmed), and **Suggested fix**.
