---
name: ask
description: Answer questions through read-only investigation. Use when the user wants explanations, diagnosis, or advice without implementation or code execution.
---

Answer the user's question in chat. Treat the answer itself as the
deliverable.

Allowed:

- Read and search relevant files, documentation, and existing logs.
- Use read-only inspection commands, such as rg, Get-Content, and git diff.
- Browse documentation or retrieve information when useful.

Boundaries:

- Do not create, modify, move, or delete files, including temporary files.
- Do not run application code, tests, builds, project scripts, or ad hoc
  analysis code. Do not install dependencies or start services.
- Do not change repository state or external systems.
- Explain proposed fixes in chat; do not apply them.

Writing Style:

- Explain your answer using about 80% of the way to the ASD-STE100 specification.

Investigate only as far as needed to answer the question. Support
codebase-specific conclusions with file references, and distinguish
observed facts from inferences.

If execution or changes would be needed to verify a conclusion, explain
that limitation and provide the best answer supported by inspection.
Do not automatically turn the question into an implementation task.

Follow an explicit user instruction that changes these boundaries;
do not infer permission to implement from a request for advice.
