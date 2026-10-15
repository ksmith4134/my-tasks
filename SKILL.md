---
name: my-tasks
description: Create, inspect, plan, select, and execute repository-local coding tasks stored in numbered ./tasks directories with TASK.md files. Use for task management and resuming work, with human plan approval before implementation, independent of coding agent or Git hosting provider.
---

# Tasks

Use `./tasks` as the repository-local execution queue and history for coding work.

Use the capabilities available in the current coding environment. Do not depend on a specific agent, IDE, or Git hosting provider.

Each task is self-contained. Treat its `TASK.md` as the source of truth for metadata, instructions, and the approved plan. Do not use a global `statuses.json`.

## Repository structure (required)

Use this structure:

```text

./tasks/
├── README.md                         # optional agent-facing roadmap
├── 0001_first_task/
│   ├── TASK.md
│   └── ...optional task assets
├── 0002_second_task/
│   └── TASK.md
└── ...
```

Name task directories:

```text
NNNN_task_name
```

Rules:

- Use a unique positive integer task ID.
- Zero-pad IDs to at least 4 digits in directory names: `0001`, `0002`, `0014`.
- Allow IDs to grow beyond 4 digits naturally, for example `10000_task_name`.
- Use a short lowercase `snake_case` task name.
- Keep the numeric `id` in `TASK.md` unpadded.
- Keep task-specific assets inside that task directory when practical.
- Never reuse an old task ID, including IDs belonging to completed tasks.

## TASK.md schema

Use YAML frontmatter followed by the task specification.

```markdown
---
id: 1
name: task_title
description: Here is the task description.
status: do-not-start
created: 2026-09-29
plan_approved: null
started: null
completed: null
git_issue: null
git_pr: null
---

# Task title

## Objective

Describe the intended outcome.

## Requirements

- Requirement n
- Requirement n+1

## Plan

Record the proposed approach, research answers, and validation strategy here before implementation. Await human approval.

## Acceptance criteria

- [ ] Criterion n.
- [ ] Criterion n+1.

## Implementation notes

Record implementation decisions or blockers as needed.

## Validation

Leave blank until validation is performed.
```

Required metadata:

- `id`: unique positive integer matching the directory ID.
- `status`: one of `do-not-start`, `start`, `in-progress`, `blocked`, `done`.
- `created`: creation date in `YYYY-MM-DD` format.

Optional metadata:

- `name`: short task name; prefer lowercase `snake_case` for new tasks.
- `description`: brief summary of the intended outcome.
- `plan_approved`: human approval date (`YYYY-MM-DD`) for the current Plan, or `null` until approved.
- `started`: date implementation began, or `null` until then.
- `completed`: completion date, or `null` until done.
- `git_issue`: same-project issue number when known, otherwise `null`.
- `git_pr`: same-project pull request or merge request number when known, otherwise `null`.

Preserve additional user-defined metadata fields unless the user asks to remove them.

## Status lifecycle and ownership

- `do-not-start`: deferred by the user; excluded from automatic selection. Reading and requested definition edits are allowed.
- `start`: selected by the user or queued for work; planning or plan approval may still be pending. This status alone does not approve a plan.
- `in-progress`: approved implementation has begun. The agent sets this before editing project code and sets `started` if empty.
- `blocked`: planning or implementation cannot proceed without a dependency, decision, or external fix. Record the blocker and what would unblock it in Implementation notes.
- `done`: all required work and relevant validation succeeded. The agent sets `completed` when marking it done.

Normal implementation lifecycle:

```text
do-not-start -> start -> (plan approved) -> in-progress -> done
```

"Work on task 12" or "implement task 12" selects that task, moving `do-not-start` to `start`, but does not bypass plan approval. Do not independently queue deferred tasks.

A `start` or `in-progress` task may become `blocked`. When explicitly revisiting it, verify the blocker is resolved, then return it to `start` if implementation never began, otherwise `in-progress`; recheck plan approval before editing code. Preserve existing dates and approval unless the plan changes materially.

Waiting for initial plan approval leaves a task at `start`, not `blocked`; replanning begun work leaves it `in-progress` with implementation paused. Fixable implementation or validation failures remain `in-progress`; use `blocked` only when progress requires outside help. Never mark incomplete or failing work `done`.

## Plan before implementation

Every task needs a human-approved plan before implementation, including tasks with no research questions.

1. Read the task, relevant assets, and affected repository code. Answer research questions wherever they appear in the task.

2. Check whether the current scope already has an approved Plan. Reuse approval recorded in the task or explicitly given in the conversation; status alone is not evidence of approval.

3. If approval is missing or the scope/approach changed materially, propose a concise plan covering the approach, important decisions, and validation. Scale detail to the task. Record it under Plan and leave `plan_approved: null`.

4. Present the plan and wait for explicit human approval before implementation. A request for revisions is not approval. Record approval in `plan_approved` only after the user grants it.

5. Proceed with the approved plan. Routine implementation choices within that plan do not require renewed approval.

Use a planning mode if the current environment provides one, but do not rely on any specific mode or command. Follow the same planning and approval gate in every environment. If file edits are unavailable, present the plan in chat and save the plan and approval when edits are allowed, before implementing.

## Select the next task

When the user explicitly identifies a task, use that task subject to the authorization rules above.

When the user asks to work on the next available task without identifying one:

1. Scan `./tasks/\*/TASK.md`; check metadata, unique numeric IDs, and directory/ID agreement. Report inconsistencies rather than guessing when selection is ambiguous.

2. Exclude `do-not-start`, `blocked`, and `done` tasks. Briefly report any skipped blockers.

3. Resume the lowest-ID `in-progress` task; otherwise select the lowest-ID `start` task.

4. Check its plan approval. If missing or outdated, present a plan and wait; otherwise implement.

5. If none qualify, report that no task is available. Do not automatically reactivate blocked or deferred tasks.

An explicitly named task takes precedence over this ordering, but must still have valid metadata, resolved blockers, and an approved plan before implementation.

## Work a task

For an authorized task:

1. Read the complete task specification and relevant assets.

2. Inspect the repository to understand the affected code and existing conventions.

3. Follow Plan before implementation. Once approved, set `in-progress` and set `started` to today's date if empty, before editing project code.

4. Implement only the work needed to satisfy the task.

5. Add or update tests when appropriate.

6. Run the relevant repository checks, tests, linting, type checks, builds, or other validation required by the task and repository.

7. Re-read the task requirements and acceptance criteria.

8. Check off an acceptance criterion only after verifying it.

9. Record concise implementation notes, including important files changed and non-obvious design decisions.

10. Record the validation commands performed and their outcomes.

11. Mark the task `done` only when all required work and relevant validation succeed.

12. Set `completed` to the current date when marking the task `done`.

Do not rewrite the user's Objective, Requirements, or Acceptance criteria merely to make completed work appear compliant. Update those sections only when the user asks to change the task definition or when clarification is necessary and explicitly authorized.

## Create a task

When the user asks to create a task:

1. Inspect existing task directories.

2. Read existing task IDs from directory names and TASK.md frontmatter when needed.

3. Choose `max(existing task IDs) + 1`; use `1` if there are no tasks.

4. Never reuse a deleted or completed task ID when prior repository history makes that ID apparent.

5. Create a concise lowercase `snake_case` task name.

6. Create `./tasks/NNNN_task_name/TASK.md` using the schema above.

7. Write a clear Objective, Requirements, and testable Acceptance criteria from the user's request.

8. Set `created` to the current date.

9. Set `plan_approved: null`, `started: null`, and `completed: null` initially.

10. Set `git_issue` when the user supplied or clearly identified a same-project Git issue; otherwise use `null`.

11. Set `git_pr` when the user supplied or clearly identified a same-project pull request or merge request; otherwise use `null`.

12. Default `status` to `do-not-start`; use `start` when the user asks to begin work.

If the user asks to create and immediately work on the task:

Create it as `start`, then follow the planning, approval, implementation, and completion workflow. Creating a task does not approve its plan.

## Find, read, or summarize tasks

Find tasks by ID, name, directory, metadata, or TASK.md content as needed.

For task status, trust that task's YAML frontmatter.

For requirements and acceptance criteria, trust that task's `TASK.md` body.

Do not infer implementation authorization merely because a task exists.

## Update a task definition

When the user asks to change a task definition:

1. Locate and read the existing `TASK.md`.
2. Preserve unrelated metadata and content.
3. Make only the requested definition changes.
4. Do not change status unless the user requests it or the normal implementation lifecycle requires it.
5. Keep acceptance criteria concrete and verifiable.
6. If a definition change invalidates the approved plan, clear `plan_approved` and obtain approval for the revised plan before further implementation.

## Completed task history

Keep completed task directories and TASK.md files in the repository.

Do not delete or archive a completed task merely because it is done. The task specification, implementation notes, validation record, code diff, commits, and pull/merge requests together form useful project history.

If the user explicitly wants an archive strategy later, preserve IDs and history when implementing it.

## Git linkage

Treat the repository's Git hosting provider as an optional human-facing planning and collaboration layer above repository tasks.

Use `git_issue` and `git_pr` only as references; neither is required. Store same-project issue and pull/merge request numbers (project-local IDs), not URLs or provider-global IDs.

A Git issue may correspond to one or many repository tasks. Do not assume completing one repository task closes the linked issue unless that task clearly completes the entire issue.

When a pull request or merge request is created or identified for a task, record its number in `git_pr`. A request may cover multiple tasks. Do not create one unless authorized by the user.

When preparing a commit or pull/merge request, preserve the distinction between:

- `TASK.md`: the agent execution specification and repository-local history.
- Git issue: broader planning, discussion, ownership, or feature tracking.
- Pull/merge request: review and delivery of implemented code.

Use the provider's automatic-closing syntax only when the pull/merge request actually completes that whole issue.

## Concurrency and safety

Avoid unnecessary shared bookkeeping files. Modify only the selected task's TASK.md plus the project files required by that task.

Per-task status reduces merge conflicts but is not a distributed lock. When multiple agent workers operate concurrently from separate branches, worktrees, or sessions, assign each worker an explicit task ID. Do not rely on multiple workers independently running the "next task" selection algorithm, because they can select the same task from the same starting state.

Before changing a task's status, re-read that task's current TASK.md so recent changes are not silently overwritten.

If the task has changed materially since it was selected, reconcile the change before continuing.

Do not overwrite unrelated user changes in TASK.md or project files.
