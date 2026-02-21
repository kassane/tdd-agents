# TDD Skill for Claude Code

A Claude Code skill that guides you through strict **Test-Driven Development** using Red-Green-Refactor cycles with human-in-the-loop pauses at every step.

## Usage

```
/tdd path/to/plan.md
```

Provide a markdown file describing the feature you want to build. The skill will break it into small, testable increments and walk you through TDD for each one.

## How It Works

### 1. Setup
- Reads your plan/feature file
- Detects your language and test framework (Python/pytest, TypeScript/vitest, Go/testing)
- Decomposes the feature into small increments
- Presents the increment list for your approval

### 2. TDD Loop (per increment)

| Phase | What happens |
|-------|-------------|
| **RED** | Proposes a failing test, writes it to a temp file for you to review/edit, then runs it to confirm it fails |
| **GREEN** | Proposes minimal code to make the test pass, writes it to a temp file for review, then runs the full suite |
| **REFACTOR** | Suggests improvements (duplication, naming, extraction), re-runs tests after changes |

You stay in control at every step — the skill pauses and asks before proceeding.

### 3. Wrap-up
- Summarizes what was built (increments, tests, final status)
- Suggests remaining work (edge cases, integration tests, docs)

## Supported Languages

| Language | Test Framework | Runner |
|----------|---------------|--------|
| Python | pytest | `python -m pytest -xvs` |
| TypeScript | vitest | `npx vitest run` |
| Go | testing (stdlib) | `go test -v ./...` |

## Key Principles

- **You write the code** — the skill proposes, you decide
- **One behavior per test** — tests stay focused and readable
- **Simplest case first** — degenerate/edge cases before the general case
- **Full suite on green** — all tests run at every green/refactor step, not just the new one
- **Proposals go to temp files** — never writes directly to your source files

## Installation

Copy this directory to `~/.claude/skills/tdd/` (or wherever your Claude Code skills live). The skill is available as `/tdd` in any Claude Code session.
