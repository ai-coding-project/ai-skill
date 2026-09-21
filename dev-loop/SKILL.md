---
name: dev-loop
description: "Use a token-efficient Chinese two-agent development loop for code-change requests: main agent plans, subagent reviews, user approves before edits, tests must pass, final output includes a short commit message."
---

# Lean Dev Loop

Use for code-change requests. Keep context small.

- All user-facing communication must be in Chinese.
- Use one skeptic subagent when possible.
- Do not edit files before user approval.

## Roles

- Main agent: plan, implement, test, deliver.
- Skeptic subagent: review plan and diff; do not own code.
- If subagents are unavailable or the change is trivial, main agent self-checks with the same criteria.

## Workflow

1. Orient:
   - Read project-root `README.md` or `readme.md`, plus project-root `AGENTS.md`, before inspecting feature files.
   - If missing, note briefly and use nearest project docs.
   - Use `README.md` structure to inspect only relevant directories.

2. Plan and approve:
   - Clarify only if ambiguity would change the implementation.
   - Main agent drafts a short plan: target files, change shape, tests.
   - Skeptic subagent reviews:
     - Is the plan feasible?
     - Is the change small enough?
     - Is this the best low-risk change?
     - Are target files and edge cases correct?
     - Are tests sufficient?
     - Will performance avoid major regression?

3. Implement narrowly:
   - Follow existing project patterns.
   - Avoid unrelated refactors and unrelated file reads.

4. Test gate:
   - Every code-modifying request must run relevant tests.
   - The task is not complete until tests all pass.
   - If tests fail, fix and rerun until passing.
   - If tests cannot be run because of environment or permission limits, report the blocker and do not claim completion.

5. Review diff:
   - Skeptic subagent checks changed files and test results against the same criteria: feasible, small, best low-risk change, correct edge cases, sufficient tests, no major performance regression.
   - Main agent fixes accepted findings and reruns tests until all pass.

6. Final output:
   - Summarize changes and tests briefly.
   - Provide one manual commit message, no more than 10 Chinese characters or 10 English words.
