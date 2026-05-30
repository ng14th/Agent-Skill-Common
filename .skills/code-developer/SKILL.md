---
name: code-developer
description: Provide a strict implementation workflow for coding agents.
  Build scoped, maintainable, testable code while following local {AGENT} guidelines.
---

# Code Developer

Use this skill when writing or changing production code.

## Required Reference

Before implementing, find and read the {AGENT} guideline file. Do not rely on a hardcoded absolute path.

Search in this order:

1. `.{AGENT}.md` in the system {AGENT} directory (`${AGENT}_HOME`, or `~/.{AGENT}` when `{AGENT}_HOME` is not set).
2. `{AGENT}.md` in the system {AGENT} directory (`${AGENT}_HOME`, or `~/.{AGENT}` when `{AGENT}_HOME` is not set).
3. `.{AGENT}.md` in the current workspace or repo root.
4. `{AGENT}.md` in the current workspace or repo root.
5. `.{AGENT}.md` or `{AGENT}.md` next to this skill file, if available.

The discovered {AGENT} guideline file is the higher-priority local standard for:

- thinking before coding;
- keeping changes simple;
- making surgical edits;
- defining verification criteria;
- writing Markdown implementation plans when requested.

If this skill and the discovered {AGENT} guideline file conflict, follow the {AGENT} guideline file.

If no {AGENT} guideline file exists, continue with this skill and mention that no local {AGENT} guideline file was found.

## Operating Loop

1. **Understand**
   - Identify the exact user request, expected behavior, current behavior, and constraints.
   - Read the relevant code paths before proposing or editing.
   - State assumptions when they affect the implementation.
   - Ask only when missing information would make a reasonable implementation risky.

2. **Scope**
   - Touch only files directly required by the task.
   - Match existing project patterns, naming, formatting, and test style.
   - Do not introduce new frameworks, broad abstractions, or unrelated refactors unless the task requires them.
   - If a simpler fix is enough, use it.

3. **Design**
   - Define the smallest production-ready change that satisfies the request.
   - Check data flow, error paths, dependency boundaries, and performance impact.
   - Prefer explicit, readable code over clever shorthand.
   - Add abstractions only when they remove real duplication or complexity.

4. **Implement**
   - Keep functions focused and easy to test.
   - Use domain-specific names that explain intent.
   - Avoid unclear identifiers such as `u`, `a`, `obj`, `tmp`, `data2`, or `handler1`.
   - Handle realistic null, empty, invalid, timeout, and failure cases.
   - Avoid hidden side effects, global state changes, and unclear control flow.
   - Add comments only when they explain non-obvious reasoning.

5. **Verify**
   - Run the narrowest useful test or check first.
   - Add or update tests for changed behavior when practical.
   - Include happy path, edge cases, and failure branches when the change affects logic.
   - Run broader checks when touching shared contracts, core utilities, or user-facing flows.
   - If verification cannot run, report the exact reason and the command that should be run.

6. **Report**
   - Summarize the concrete change, files touched, and verification result.
   - Mention residual risks or skipped checks.
   - Do not oversell the change or hide uncertainty.

## Quality Gates

Before finishing, confirm:

- The implementation directly maps to the user request.
- No unrelated files or formatting were changed.
- The code follows local project conventions.
- Names are clear and domain-oriented.
- Failure cases are handled at the correct layer.
- Tests or checks were run, or a concrete reason is given for not running them.
- New complexity is justified by an actual current need.

## Plan File Rule

When the user asks for an implementation plan, create or update a Markdown plan file instead of only answering in chat.

Use the structure required by the discovered {AGENT} guideline file:

- Problem
- Solution
- Handling Plan
- Verification Run Set
- Results and Report
