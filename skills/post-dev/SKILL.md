---
name: post-dev
description: Run after development is complete to perform comprehensive quality checks before handing back to the user. Spawns parallel agents for code review, file-level refactoring opportunities, documentation updates (walking up the directory tree), and test verification. Use when the user says /post-dev or indicates they're done coding and want a final quality pass.
---

# Post-Dev Quality Gate

Run four parallel quality checks on all files changed in the current branch (compared to `main`). Report findings and fix issues before handing back to the user.

## Step 1: Identify Changed Files

Run `git diff --name-only main...HEAD` to get the list of all files changed on this branch. This is the input for all four agents.

## Step 2: Spawn Four Parallel Agents

Launch all four agents simultaneously using the Agent tool. Each agent gets the list of changed files and works independently.

### Agent 1: Code Review (New Code)

**Goal:** Comprehensive review of all new and modified code on this branch.

Prompt the agent with:
- The list of changed files
- Instructions to review each file's diff (`git diff main...HEAD -- <file>`) for:
  - Bugs, logic errors, edge cases
  - Security issues (injection, auth, data exposure)
  - Performance concerns
  - Code style consistency with surrounding code
  - Missing error handling at system boundaries
  - Unnecessary complexity or dead code introduced
- Report issues grouped by severity (critical / warning / nit)
- For each issue: file path, line range, what's wrong, suggested fix
- Fix any critical issues directly; report warnings and nits without fixing

### Agent 2: File-Level Refactoring Opportunities

**Goal:** For each file touched on this branch, review the ENTIRE file (not just the diff) for broader improvement opportunities.

Prompt the agent with:
- The list of changed files
- Instructions to read each full file and assess:
  - Functions that are too long or do too many things
  - Duplicated logic that could be extracted
  - Naming that could be clearer
  - Dead code or unused imports
  - Patterns inconsistent with the rest of the codebase
  - Type safety improvements
- Do NOT fix these — report them as a prioritized list
- Format: file path, what to improve, why, estimated effort (quick / medium / large)

### Agent 3: Documentation Updates

**Goal:** For every file touched, walk UP the directory tree checking for documentation that needs updating.

Prompt the agent with:
- The list of changed files
- Instructions to, for each unique directory containing a changed file:
  1. Check that directory for any docs (README.md, *.md files, doc comments, CLAUDE.md)
  2. If docs exist, read them and determine if changes on this branch require updates
  3. Move to the parent directory, repeat
  4. Continue until reaching the project root
  5. At the project root, check CLAUDE.md and docs/ folder
- Update any documentation that is now stale or incomplete due to the changes
- If no docs exist in a directory, skip it (do not create new docs unless explicitly needed)
- For `docs/` folder files: update architecture docs, pipeline docs, etc. if the changes affect them

### Agent 4: Test Verification

**Goal:** Ensure all tests pass with 100% success rate.

Prompt the agent with:
- The project structure (web/ uses Jest via `npm run test`, ios/ uses XCTest via `./run-tests.sh`)
- Instructions to:
  1. Determine which test suites are relevant based on changed files
  2. Run the appropriate test commands:
     - If web files changed: `cd web && npm run test`
     - If iOS files changed: `cd ios && ./run-tests.sh`
     - If both: run both
  3. If any tests fail:
     - Analyze the failure
     - Fix the failing test or the code causing the failure (use judgment — if the test is wrong due to new behavior, update the test; if the code is wrong, fix the code)
     - Re-run until green
  4. Report: which suites ran, pass/fail counts, any fixes applied

## Step 3: Collect Results

After all agents complete, present a unified summary to the user:

```
## Post-Dev Summary

### Code Review
[critical issues fixed / warnings found / nits]

### Refactoring Opportunities
[top 3-5 items worth addressing, if any]

### Documentation
[files updated / no updates needed]

### Tests
[all passing ✓ / fixes applied]
```

Keep the summary concise. Link to specific files/lines for anything the user should look at.

## Important Notes

- Do NOT ask the user questions during this process — run autonomously and report results
- Fix critical code review issues and failing tests directly
- Do NOT fix refactoring items (report only) unless they're trivial one-liners
- Documentation updates should be minimal and accurate — don't pad docs
- If no files changed vs main, report that and exit early
