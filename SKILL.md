---
skill: tdd
trigger: "Use when the user invokes /tdd or asks to do TDD on a feature/plan file. Guides the developer through Red-Green-Refactor cycles with human-in-the-loop pauses at each step."
arguments:
  - name: plan_file
    description: "Path to a markdown file describing the feature/plan to implement via TDD"
---

# TDD Skill — Red-Green-Refactor with Human in the Loop

You are guiding the developer through strict Test-Driven Development. The developer writes all code — you propose, they decide. Pause at every step for review.

## Phase 1: Setup

1. Read the plan/feature file the user provided.
2. Detect the language and test framework from the project. If ambiguous, ask:
   - Python → pytest
   - TypeScript → vitest
   - Go → testing (stdlib)
   See `references/language-configs.md` for runner commands and conventions.
3. Break the feature into small, testable increments. See `references/increments.md` for decomposition patterns.
4. Create a task list using `TaskCreate` — one task per increment.
5. Present the increment list to the user with `AskUserQuestion`:
   - "Here are the increments I've identified. Want to reorder, add, remove, or modify any before we start?"
   - Options: "Looks good, let's start" / "I want to modify the list"

## Phase 2: TDD Loop

For each increment, follow Red-Green-Refactor strictly. Mark the current increment as `in_progress` via `TaskUpdate`.

### RED — Write a Failing Test

1. Propose a failing test. Write it to a temporary test file (e.g., `tests/test_proposed.py` or the equivalent for the project's test directory) so the user can review and edit it in their editor. Also show the test code in a fenced code block for quick reference. Explain what behavior it verifies and why this is the right next step.
2. Pause with `AskUserQuestion`:
   - "I've written the proposed test to `<temp_test_file>`. Review/edit it in your editor, then tell me when you're ready to run it."
   - Options: "Ready, run the test" / "I have questions first"
3. When the user says ready, run the test using the appropriate runner (see language-configs.md). Use `Bash`.
4. Confirm the test **fails**. If it passes unexpectedly, flag this:
   - "The test passed already — this means either the behavior is already implemented or the test isn't asserting the right thing. Let's investigate before moving on."

### GREEN — Write Minimal Code to Pass

5. Propose the **minimal** production code to make the failing test pass. Write it to a temporary file (e.g., `src/proposed_solution.py` or equivalent) so the user can review and edit it. Also show it in a fenced code block. Emphasize: write only enough to pass, nothing more.
6. Pause with `AskUserQuestion`:
   - "I've written the proposed code to `<temp_code_file>`. Review/edit it, then move it to the right location when ready."
   - Options: "Ready, run the tests" / "I have questions first"
7. When the user says ready, run **all** tests (not just the new one). Use `Bash`.
8. Confirm all tests **pass**. If any fail, help debug:
   - Read the failure output carefully.
   - Suggest a fix. Do NOT write it — let the user apply it.
   - Re-run tests after they signal ready.

### REFACTOR — Improve the Code

9. Review the current code. Suggest refactoring opportunities if any exist:
   - Duplication removal
   - Better naming
   - Extract method/function
   - Simplify conditionals
   If no refactoring is needed, say so explicitly.
10. If refactoring is suggested, pause with `AskUserQuestion`:
    - "I've suggested some refactoring above. Apply what you agree with, skip what you don't, then tell me when you're ready to run tests."
    - Options: "Ready, run the tests" / "Skip refactoring, move on" / "I have questions"
11. If refactoring was done, run all tests again to confirm still green.

### NEXT

12. Mark the increment as `completed` via `TaskUpdate`.
13. Briefly summarize: what test was added, what code was written, what was refactored.
14. Move to the next increment. Go back to RED.

## Phase 3: Wrap-up

After all increments are complete:

1. Summarize what was built:
   - List each increment and its test
   - Note the final test count and all-passing status
2. Suggest any remaining work (edge cases, integration tests, documentation).

## Rules

- **Write proposals to temporary files** (e.g., `tests/test_proposed.py`, `src/proposed_solution.py`) so the user can review and edit them in their editor. Never write directly to the final source/test files — the developer moves or merges the proposed code when ready.
- **Every pause is an `AskUserQuestion` call.** Do not continue without explicit user go-ahead.
- **Run the full test suite** at Green and Refactor steps, not just the new test.
- **Keep tests behavior-focused.** Test what the code does, not how it does it.
- **Simplest case first.** Start with the degenerate/edge case, build toward the general case.
- **One assertion per test** when possible. Each test should verify one behavior.
- **Track progress visually** using TaskCreate/TaskUpdate so the user sees where they are.
