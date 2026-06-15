---
name: code-reviewer
description: Provide evidence-based, multi-axis code review before merging or after completing any change. Use when reviewing code written by yourself, another agent, or a human; assess correctness, readability, architecture, security, performance, tests, change size, dependencies, and verification; produce findings-first output and a P1-P5 remediation plan while following local CODEX/AGENTS guidelines.
---

# Code Reviewer

Review code as a quality gate. Approve only when the change clearly improves overall code health, follows project conventions, and has no blocking correctness, security, data-loss, or verification issues. Do not block only because the code differs from personal preference.

## Required References

Before reviewing, find and read local guidance. Do not rely on a hardcoded absolute path.

Search in this order:

1. `$CODEX_HOME/AGENTS.md`, `$CODEX_HOME/CODEX.md`, or `$CODEX_HOME/.CODEX.md` when `$CODEX_HOME` is set.
2. `~/.codex/AGENTS.md`, `~/.codex/CODEX.md`, or `~/.codex/.CODEX.md`.
3. `AGENTS.md`, `CODEX.md`, or `.CODEX.md` in the current workspace or repo root.
4. `AGENTS.md`, `CODEX.md`, or `.CODEX.md` next to this skill file, if available.

Use discovered guidance to constrain review scope, assumptions, prioritization, and recommendations. If local guidance conflicts with this skill, follow local guidance for behavioral rules and preserve this skill's findings-first report shape where possible. If no guidance file exists, continue and mention that none was found.

Local guidance should especially enforce:

- Review only changed files and directly related code paths.
- Avoid unrelated refactor requests.
- Separate confirmed findings from assumptions.
- Prefer simple fixes over speculative architecture changes.
- Require evidence from exact files, functions, lines, tests, or behavior.

## P1-P5 Review Workflow

### P1: Understand Context

Establish what the change is trying to accomplish before judging implementation details.

- Identify the task, spec, bug, PR description, or expected behavior change.
- Check whether the change is one logical unit. If it mixes feature work and refactoring, flag the review risk.
- For large changes, recommend splitting unless the size is justified by generated code, deletions, or mechanical refactoring.

Change size guidance:

```text
~100 lines changed   -> Good
~300 lines changed   -> Acceptable for one logical change
~1000 lines changed  -> Too large; ask to split unless mostly mechanical
```

### P2: Review Tests First

Use tests to understand intent and coverage before reviewing implementation.

- Confirm tests exist for the changed behavior.
- Check that tests assert behavior, not only execution.
- Look for happy path, edge cases, invalid/null/empty inputs, error paths, and regression coverage for bug fixes.
- Verify tests are deterministic, isolated, and named clearly.
- Record what was or was not run.

### P3: Review Implementation Across Five Axes

Evaluate each changed file and directly related path across these axes:

1. Correctness
   - Matches the task/spec and expected behavior.
   - Handles null, empty, boundary values, off-by-one cases, race conditions, state consistency, and error propagation.
   - Avoids happy-path-only logic and unsafe assumptions.

2. Readability and simplicity
   - Names are descriptive and consistent with local conventions.
   - Control flow is straightforward.
   - Functions are appropriately sized and grouped.
   - Abstractions earn their complexity; do not generalize before a real repeated need.
   - Comments clarify non-obvious intent and do not restate obvious code.
   - Formatting, spelling, grammar, syntax, and language-specific conventions match the project.

3. Architecture
   - Fits existing patterns or clearly justifies a new one.
   - Preserves module boundaries and dependency direction.
   - Avoids circular dependencies, unnecessary coupling, speculative flexibility, and duplicated logic that should be shared.
   - Separates refactoring from behavior changes unless the cleanup is small and local.

4. Security
   - Secrets, tokens, PII, and environment variables are handled safely and not exposed in code, logs, commits, configs, or errors.
   - Authentication and authorization checks are correct.
   - User input and external data are treated as untrusted at boundaries.
   - SQL/command/template/HTML injection risks are controlled with parameterization, escaping, or safe APIs.
   - Dependencies are trusted, compatible, maintained, and vulnerability-checked when relevant.

5. Performance
   - No N+1 queries, unbounded loops, unconstrained fetches, missing pagination, or avoidable nested work in hot paths.
   - Algorithmic complexity is appropriate for realistic data sizes.
   - CPU, memory, I/O, rendering, database, and concurrency costs are acceptable.
   - Synchronous work is not blocking async or UI-sensitive paths unnecessarily.

### P4: Categorize Findings

Label every finding by priority and make required action clear.

Priority plan:

- P1: Must fix now. Broken functionality, data loss, security vulnerability, privacy leak, corrupt state, or merge-blocking correctness issue.
- P2: Should fix before merge. Reliability, important edge-case, performance, scalability, authorization, or maintainability issue likely to matter soon.
- P3: Test or verification gap. Missing regression tests, weak assertions, missing build/test proof, or manual verification gap.
- P4: Cleanup. Readability, style, naming, small architecture cleanup, dead code, or dependency hygiene that improves maintainability.
- P5: Optional/FYI. Non-blocking suggestion, future improvement, context, or preference-level nit.

Inline comment labels may use `Critical`, `Important`, `Nit`, `Optional`, `Consider`, and `FYI` when that is the local convention. Map them back to the P1-P5 plan in the final report.

Dead code hygiene:

- Identify unreachable, unused, or obsolete code created by the change.
- Ask before deleting code unless the user explicitly asked you to clean it up.
- Do not flag unrelated historical dead code as a required fix; mention it separately only if useful.

Dependency discipline:

- Prefer standard library and existing project utilities over new dependencies.
- For each new dependency, check whether the existing stack solves the problem, bundle/runtime impact, maintenance status, known vulnerabilities, and license compatibility.

### P5: Verify And Report

Verify the verification story, then report findings first.

- State tests, builds, linters, type checks, audits, screenshots, benchmarks, or manual flows that were run.
- If a relevant check was not run, say why.
- Do not rubber-stamp with "LGTM" without evidence.
- Do not soften real production risks. Quantify impact when possible.
- Accept informed author override gracefully, but keep the technical record clear.

## Output Format

Use this structure. Keep it concise and evidence-based.

```md
# Code Review Results

## Findings
- P1: [file:line] Problem. Impact. Fix.
- P2: [file:line] Problem. Impact. Fix.

If no findings:
No blocking findings found.

## Open Questions / Assumptions
- ...

## Suggested Update Plan (P1-P5)
- P1: ...
- P2: ...
- P3: ...
- P4: ...
- P5: ...

## Verification
- Checked: ...
- Not run: ...

## Summary
- Strengths: ...
- Residual risk: ...
```

## Rules

- Be specific and cite exact files, functions, lines, tests, or behavior.
- Lead with bugs, risks, regressions, and missing tests; keep praise secondary.
- Prefer actionable fixes over general advice.
- Separate confirmed issues from assumptions.
- Keep scope surgical: changed files and directly related code paths.
- Do not request speculative architecture work unless it is needed for correctness, security, performance, or maintainability of this change.
- Do not accept "fix later" for P1/P2 issues unless there is an explicit emergency and tracked follow-up.
