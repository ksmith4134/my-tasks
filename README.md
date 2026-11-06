# My Tasks

Five skills for planning, building, reviewing, and testing coding tasks inside a repository.

Tasks live in `./tasks/` in your repository, one folder per task, each with a `TASK.md`. Nothing is stored outside the repository, so the skills work with GitHub, GitLab, or any other Git host, and in any editor.

## The skills

| Skill | What it does | Writes |
| --- | --- | --- |
| `my-tasks` | The rules for tasks: how to create, number, plan, and finish them. Every task needs your approval of its plan before any code changes. | `tasks/NNNN_name/TASK.md` |
| `critique-plan` | Reviews a task's plan for gaps and mistakes. Does not change anything else. | `critique-plan.md` |
| `review-code` | Reviews the code written for a task. Does not fix anything. | `code-review.md` |
| `test-plan` | Runs the task's tests and checks. Does not fix anything. | `test-report.md` |
| `run-task` | **Claude only.** Runs the whole pipeline, with Claude building and Codex reviewing. | (uses the others) |

The three review skills work in any agent. Each one adds a new round to its report, commits and pushes it, comments on the pull/merge request, and ends with a one-line verdict.

`my-tasks/task-template.md` is a prompt for running a whole task in one agent without `run-task`.

## Requirements

Set these up in each project before installing:

1. **`.claude/skills` is a symlink to `.agents/skills`.** Codex reads `.agents/skills` and Claude Code reads `.claude/skills`, so the symlink lets both agents use the same copy.

   ```bash
   # macOS / Linux
   mkdir -p .agents/skills .claude
   ln -s ../.agents/skills .claude/skills
   ```

   ```powershell
   # Windows PowerShell (needs Developer Mode or an admin shell)
   New-Item -ItemType Directory -Force .agents\skills, .claude
   New-Item -ItemType SymbolicLink -Path .claude\skills -Target (Resolve-Path .agents\skills)
   ```

2. **`CLAUDE.md` points to `AGENTS.md`.** `CLAUDE.md` contains only:

   ```text
   @AGENTS.md
   ```

3. **`AGENTS.md` tells the agents how to work in your project.** The skills look here for:
   - the commands for offline checks (tests, lint, type checks, build)
   - which live services or real-world actions are allowed, if any; the skills treat them as not allowed unless `AGENTS.md` or you say otherwise
   - rules for credentials and personal data
   - links to any development or testing guides

4. **For `run-task`:** Claude Code and Codex, plus the Git host's CLI (`gh` for GitHub, `glab` for GitLab) installed and signed in, so the agents can open and comment on pull/merge requests.

## Install

From the project folder:

```bash
npx skills add ksmith4134/my-tasks            # choose from all five
npx skills add ksmith4134/my-tasks --skill run-task   # just one
npx skills add ksmith4134/my-tasks --list     # show what's available
```

When asked which agent to install for, choose Codex or Claude Code. Because of the symlink, both agents see the skills either way.

## Using it

Ask your agent to:

- Create a task
- Show available tasks
- Work on task 1
- Work on the next available task
- Run critique-plan, review-code, or test-plan on task 1

In Codex, call a skill with `$name`, for example `$review-code 4`. In Claude Code, use `/name`, for example `/run-task 4`.

## How `run-task` works

You run `/run-task <N>` in Claude Code. The pipeline:

1. Claude interviews you, writes the plan in `TASK.md`, and opens a pull/merge request.
2. Codex critiques the plan.
3. Claude revises the plan, then **stops for your approval**.
4. Claude builds the task and writes `test-plan.md`.
5. Codex reviews the code, and Claude fixes confirmed problems.
6. Codex runs the tests, and Claude fixes failures. The tests are rerun at most twice.
7. Claude reports the results and **stops for your final approval**. Merging is up to you.

Claude passes work to Codex in one of two ways:

- **Without T3 Code (default):** Claude pushes, comments on the pull/merge request with the exact command for Codex, and stops. You run that command in Codex. Codex commits its report and comments its verdict. Then you go back to Claude and run `/run-task <N>` or say "continue". Claude pulls and carries on.
- **With T3 Code:** Claude starts Codex itself and waits for it. You only step in at the two approval points.

If you stop partway through, `/run-task <N>` works out from the task files where to resume.

## Updating a project that has older copies

The installer overwrites the skill folders, so:

1. Commit the project first, including `.agents/skills/`.
2. Run the install command again.
3. Review the changes with `git diff .agents/skills`.
4. If anything project-specific was lost, move it into that project's `AGENTS.md` rather than back into the skills, so the next update doesn't remove it again.

Existing tasks and reports keep working. `git_issue` and `git_pr` in `TASK.md` are optional.

## License

MIT
