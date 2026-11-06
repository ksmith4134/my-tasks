Work on task [TASK NUMBER].

Use $my-tasks to manage the work.

1. Read TASK.md, the relevant project documents, and existing code.
   Reuse decisions and answers already recorded.

2. Interview me about unresolved requirements needed to complete
   the task. Ask a few questions at a time and wait for my answers.
   Skip questions whose answers are already established.

3. Write a concrete implementation and validation plan in TASK.md.

4. Run $critique-plan. Address supported findings in TASK.md.
   If findings expose unresolved requirements, ask me and wait
   for the answers before continuing.

5. Implement the task. This prompt grants advance approval to
   implement within the agreed task requirements and overrides
   the separate plan-approval pause. Record that approval and
   follow the task status and date requirements.

6. Run $review-code. Fix supported findings, rerun affected tests,
   and recheck the findings before considering the task complete.

7. Run $test-plan. Fix supported issues and rerun affected checks.

8. Follow the safety rules and live-check limits in AGENTS.md.

9. Record actual validation results. Mark the task done only when
   its required checks and acceptance criteria are satisfied.

10. Report what changed, what passed, any remaining limitations,
    and exactly how I can check the work, including relevant
    commands and expected outcomes.
