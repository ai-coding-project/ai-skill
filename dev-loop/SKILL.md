---
name: dev-loop
description: "Use when developing or modifying project code. Default to simple tasks handled by the Main agent. For complex tasks, use separate developer and reviewer subagents. Get user approval for the plan before edits. Tests must pass. Final output includes a Chinese commit message."
---

# Lean Dev Loop

Use for code-change requests. Keep context small.

## Rules

- All user-facing communication must be in Chinese.
- Do not edit files before user approval.
- For simple tasks, the Main agent handles the task alone; no subagent is needed.
- For complex tasks, use subagents called by the Main agent. Development and review must be separate.
- If scope, public API, data, security, or test strategy changes after approval, ask the user for re-approval.

## Roles

- Main agent: Understand requirements, decide simple/complex, plan, call subagents for complex tasks, integrate, and deliver. For simple tasks, handle implementation and testing alone.
- Developer subagent (complex tasks only): Implement and run tests.
- Reviewer subagent (complex tasks only): Review the plan and diff. Do not let the same subagent develop and review its own work.
- User: Approve the plan before edits.

## Simple vs. Complex

Default to simple. The Main agent handles simple tasks alone, even if they touch multiple files, as long as they are not complex under this definition. File count alone does not make a task complex.

A task is complex only if at least one of the following is true:

- The change logic is very complex: it requires coordinated changes to multiple interdependent core flows, state machines, algorithms, concurrency logic, scheduling logic, or similar, with many branches and edge cases, so one Main agent cannot reliably complete and self-review it in one context.
- The number of changed files is too large: it requires changing more than 10 files, the changes are not mechanical replacements or bulk renames, and either effective changed lines exceed 500 or the change touches more than 3 core flows.
- The user explicitly asks for subagents or independent review.

Not complex by themselves:

- Editing multiple local files
- Local refactoring within one module
- Adding a field, fixing a bug, changing UI text, or adding ordinary tests
- Low-risk dependency updates
- Changes with clear boundaries and runnable tests

## Workflow

1. Orient
   - Read project-root `README.md` and `AGENTS.md`.
   - Read project-root `.repomap/index.md`; Use it as a project index to understand the project.
   - If either file is missing, skip it. Do not search other directories.

2. Find relevant code and Understand requirements
   - Understand the user's requirements.
   - Ignore files listed in the project-root `.gitignore`
   - Prefer LSP tools for code navigation and understanding, such as go-to-definition, find-references, document symbols, and diagnostics.
   - Do not read irrelevant code.

3. Ask clarifying questions
   - Ask questions in Chinese to confirm your understanding of key points.
   - must confirm the implementation details of all requirements with the user; do not make decisions unilaterally.

4. Plan
   - Main agent drafts a short plan: target files, change scope, tests.
   - Decide whether the task is simple or complex.
   - For complex tasks, break the work down and use separate subagents for development and review.

5. Review plan and get approval
   - For simple tasks, the Main agent self-reviews the plan.
   - For complex tasks, the reviewer subagent checks the plan.
   - Check:
     - Is the plan feasible?
     - Is the change small enough?
     - Is this the best low-risk change? If not, remind the user.
     - Are target files and edge cases correct?
     - Are tests sufficient?
     - Will it avoid major performance regressions?
   - Present the plan to the user in Chinese and wait for approval before edits.

6. Implement and test
   - Follow existing project patterns.
   - Avoid unrelated refactors and unrelated file reads.
   - Simple tasks: Main agent implements and runs relevant tests.
   - Complex tasks: developer subagent implements and runs relevant tests; Main agent confirms the reported test results.
   - Every code-modifying request must run relevant tests.
   - New features must add or modify test code.
   - Bug fixes should add regression tests when possible; if not, explain the reason and risk.
   - The task is not complete until tests all pass.
   - If tests cannot run because of environment or permission limits, report the blocker and do not claim completion.

7. Review diff
   - For complex tasks, the reviewer subagent checks changed files and test results against the same criteria: feasible, small enough, best low-risk change, correct edge cases, sufficient tests, no major performance regression.
   - The Main agent coordinates fixes and confirms reported test results until passing.

8. Final output
   - Summarize changes and tests briefly in Chinese.
   - Include files changed, test commands/results, and any blockers or risks.
   - Provide one suggested commit message in Chinese. An optional prefix is allowed and does not count toward the limit. The body must not exceed 30 Chinese characters.