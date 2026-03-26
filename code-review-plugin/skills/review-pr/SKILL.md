---
name: review-pr
description: >
  Performs structured code reviews on pull requests or code changes.
  Use when asked to review a PR, check code quality, analyze diffs,
  or provide feedback on code changes. Follows a systematic methodology
  covering correctness, readability, performance, and security.
---

# Code Review Methodology

When reviewing code, follow this structured process to ensure thorough, consistent, and actionable feedback.

## Phase 1 — Understand the Context

Before reading any code, answer these questions:

1. What is the purpose of this change? Read the PR title, description, and linked issues.
2. What area of the codebase does it touch? Identify the modules, services, or layers involved.
3. What is the expected scope? A bug fix should be small and focused. A feature may touch multiple files.

If reviewing a PR via GitHub MCP, use the `github_get_pull_request` tool to fetch the PR metadata first.

## Phase 2 — Read the Diff Systematically

Read the changes in this order:

1. **Test files first.** Tests reveal intent. If tests were added, they tell you what the author expects the code to do. If no tests were added, flag that immediately.
2. **Interface changes.** Look at API contracts, type definitions, function signatures. These are the highest-impact changes.
3. **Implementation files.** Now read the actual logic, with test expectations and interface contracts already in mind.
4. **Configuration and infrastructure.** CI changes, dependency updates, config file modifications.

Use the `Glob` tool to find relevant test files: `**/*test*`, `**/*spec*`, `**/__tests__/**`

## Phase 3 — Evaluate Against Criteria

For each file, assess these dimensions:

### Correctness
- Does the code do what the PR description claims?
- Are edge cases handled? (null values, empty collections, boundary conditions)
- Are error paths tested and handled gracefully?
- Does it introduce any regressions to existing behavior?

### Readability
- Are variable and function names descriptive and consistent with the codebase?
- Is the control flow easy to follow?
- Are there comments where the logic is non-obvious?
- Would a new team member understand this code in six months?

### Performance
- Are there unnecessary loops, redundant API calls, or N+1 query patterns?
- Could any operations be batched or cached?
- Are there large allocations inside hot paths?
- Only flag performance issues that would materially affect the system — not micro-optimizations.

### Security
- Is user input validated and sanitized before use?
- Are secrets, tokens, or credentials exposed in the diff?
- Are there SQL injection, XSS, or path traversal risks?
- Are authorization checks present where required?

For deeper security analysis, delegate to the `security-reviewer` agent using: "Run a security-focused review on these files."

## Phase 4 — Quality Gate

Before writing your review, verify:

- You have read every file in the diff (not just the ones that look interesting)
- You can explain the purpose of the change in one sentence
- You have at least one positive observation (something done well)
- If you found no issues, explicitly state that and explain why the code looks solid

## Phase 5 — Structure Your Feedback

Organize your review as:

### Summary
One paragraph: what does this PR do, and is it ready to merge?

### Critical Issues (Must Fix)
Issues that would cause bugs, security vulnerabilities, or data loss. These block merging.
Format: `[CRITICAL] file:line — description of issue and suggested fix`

### Suggestions (Should Consider)
Improvements that would make the code better but are not blocking.
Format: `[SUGGESTION] file:line — description and rationale`

### Positive Observations
At least one thing the author did well. Specific praise is more useful than generic approval.
Format: `[GOOD] file:line — what was done well and why it matters`

### Verdict
One of: **Approve**, **Request Changes**, or **Comment Only** — with a one-sentence rationale.

## Error Handling

- If you cannot access a file referenced in the diff, note it explicitly rather than guessing the content.
- If the PR is too large to review thoroughly (more than 30 files or 2000+ lines changed), state this upfront and focus on the highest-risk files first.
- If you are unsure whether something is a bug or intentional behavior, phrase it as a question rather than a directive.
