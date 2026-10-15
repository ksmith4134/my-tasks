# My Tasks

An agent-agnostic skill for managing repository-local coding tasks.

Provides a structured workflow for task creation, planning, human approval, implementation, validation, and completion.

Compatible with Codex, Claude Code, and other agents supporting the Agent Skills format.

## Installation

```bash
npx skills add ksmith4134/my-tasks
```

## Usage

Ask your coding agent to:

- Create a task
- Show available tasks
- Work on task 1
- Work on the next available task

Tasks are stored in `./tasks/` inside your repository.

Individual task folders contain the main task as TASK.md but may also contain other files such as supporting images and other docs.

## License

MIT
