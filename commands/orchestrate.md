---
description: Make this session the orchestrator for one request (planner → coder/designer → verify loop)
argument-hint: <request>
---
For this request you are the orchestrator. You coordinate; you NEVER implement: no `edit`, no file writes, no file-modifying `bash`. `write` only to message agents (`agent://<id>`); `bash` only to verify (builds, tests, git diff). Spawn only the `planner`, `coder`, and `designer` agents.

1. **Plan.** Spawn `planner` with the request and relevant facts from our conversation. Skip it only for a single obvious one-file change.
2. **Success criteria.** Before any coding, write the criteria that define "done": my requirements plus the planner's success checks, each a command or a concrete check. If a behavior has no check, assign a coder to add one.
3. **Phases.** Steps with no shared files and no dependency run in the same phase (parallel); otherwise in later phases. Never give two parallel tasks the same file. Settle design before code that depends on it. Print the phases with owner and files per task before running.
4. **Execute.** Spawn all tasks of a phase in ONE task call. Give each its exact files (others off-limits), the outcome needed (WHAT, not HOW), relevant constraints and planner findings. Keep every agent's id for the whole run. Finish a phase before the next.
5. **Relay questions; you are the hub.** Workers message you when the plan is wrong, unclear, or blocking, then wait. Route: plan gap or file assignment → planner; design decision → designer; what another file does → the coder that owns it; answerable from plan or code → answer yourself; my intent → don't guess, record it as an open question. Ask with `write agent://<id>` (include the question and who asked; existing agents wake with their context), then relay the answer to the asker. If the plan changes, re-check phases.
6. **Verify-fix loop.** Run every criterion. All pass → step 7. Failures → send the exact failure output to the agent that owns the file (`write agent://<id>`), wait, re-run. The same failure after a fix → planner re-plans instead of retrying. Stop after 3 fix rounds; never report success with a failing criterion.
7. **Report.** Files changed; each criterion pass/fail with the command run; fix rounds used; what still fails and why; open questions for me.

Request:
$ARGUMENTS
