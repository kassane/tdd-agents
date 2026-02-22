# Language & Test Framework Configurations

## Python (pytest)

- **Test runner:** `python -m pytest -xvs`
  - `-x` stop on first failure (useful in Red phase)
  - `-v` verbose output
  - `-s` show print output
- **Run single test:** `python -m pytest -xvs path/to/test_file.py::test_name`
- **Run all tests:** `python -m pytest -xvs`
- **Test file naming:** `test_*.py` or `*_test.py`
- **Test function naming:** `def test_*():`
- **Import pattern:** `from module_name import ClassName, function_name`
- **Project structure:**
  ```
  src/
    module_name/
      __init__.py
      feature.py
  tests/
    test_feature.py
  ```
- **Assertion style:** Plain `assert` statements (pytest rewrites them for good diffs)

## TypeScript (vitest)

- **Test runner:** `npx vitest run`
- **Run single test:** `npx vitest run path/to/file.test.ts -t "test name"`
- **Run all tests:** `npx vitest run`
- **Watch mode (don't use in skill):** `npx vitest`
- **Test file naming:** `*.test.ts` or `*.spec.ts`
- **Test function naming:** `test('description', () => {})` or `it('description', () => {})`
- **Import pattern:** `import { thing } from './module'`
- **Project structure:**
  ```
  src/
    feature.ts
    feature.test.ts    # co-located
  ```
  or
  ```
  src/
    feature.ts
  tests/
    feature.test.ts    # separate directory
  ```
- **Assertion style:** `expect(value).toBe(expected)`, `expect(fn).toThrow()`

## Go (testing)

- **Test runner:** `go test -v -run TestName ./path/to/package`
- **Run single test:** `go test -v -run ^TestFunctionName$ ./path/to/package`
- **Run all tests:** `go test -v ./...`
- **Test file naming:** `*_test.go` (same package directory)
- **Test function naming:** `func TestFeatureName(t *testing.T)`
- **Import pattern:** Standard Go imports
- **Project structure:**
  ```
  package/
    feature.go
    feature_test.go    # always co-located in Go
  ```
- **Assertion style:** `if got != want { t.Errorf("got %v, want %v", got, want) }`
- **Note:** Consider suggesting `testify/assert` if already in go.mod

## Zig (built-in testing)

- **Test runner:** `zig test file.zig` or `zig build test` (if using `build.zig`)
  - Common TDD flags: `zig test -freference-trace` (better stack traces on failure)
- **Run single test:** `zig test file.zig --test-filter "test name substring"`
- **Run all tests:** `zig test src/*.zig` or `zig build test`
- **Test block naming:** `test "description of behavior" { … }`
- **Import pattern:** `const std = @import("std");` / `const expect = std.testing.expect;`
- **Project structure:**
  ```
  src/
    feature.zig          # production code + tests mixed
    main.zig             # optional entry point
  build.zig              # optional, for larger projects
  build.zig.zon          # optional, for dependencies
  ```
  Tests are usually placed in the same file as the code (very idiomatic in Zig)
- **Assertion style:** `try std.testing.expect(value == expected);`  
  or `std.testing.expectEqual(expected, actual);`  
  or `std.testing.expectError(error.Expected, fnThatFails());`
- **Note:** No external test framework needed — testing is built into the language and compiler. Very lightweight and fast for Red–Green–Refactor cycle.

## Rust (built-in + cargo)

- **Test runner:** `cargo test`
  - Useful flags for TDD: `cargo test -- --nocapture` (show println!), `cargo test -q` (quiet)
- **Run single test:** `cargo test test_name_substring` or `cargo test module::test_name`
- **Run all tests:** `cargo test`
- **Test file naming:** `tests/*.rs` (integration tests) or `#[cfg(test)]` modules inside `src/*.rs`
- **Test function naming:** `#[test] fn test_feature_works() { … }`
- **Import pattern:** `use crate::module::Thing;` (inside same crate)
- **Project structure:**
  ```
  src/
    lib.rs (or main.rs)
    feature.rs
    feature/
      mod.rs
  tests/
    integration_test.rs     # integration tests (use crate as external)
  ```
  Unit tests are typically inside production files (co-located) using `#[cfg(test)] mod tests { … }`
- **Assertion style:** `assert_eq!(actual, expected);` or `assert!(condition);`  
  Many people add `anyhow`/`thiserror` for better error testing, or use crates like `insta`, `proptest` later
- **Note:** Rust’s ownership & borrow checker already prevents many bugs → TDD often focuses more on logic, edges, and invariants than in dynamic languages.

## D (Dlang, dub + built-in unittests)

- **Test runner:** `dub test`
  - Builds and runs all `unittest` blocks
- **Run single test:** Not built-in easily; use `--run` with filters or third-party runners (e.g. unit-threaded)
- **Run all tests:** `dub test` or `dub test --config=unittest`
- **Test block naming:** `unittest { … }` (can appear anywhere in module)
- **Import pattern:** `import module_name : SymbolName;`
- **Project structure:**
  ```
  source/
    app.d              # or lib.d
    feature.d          # production + unittest blocks
  dub.json / dub.sdl
  ```
  Tests are co-located inside production modules (very idiomatic)
- **Assertion style:** `assert(condition, "optional message");`  
  `assert(a == b);` is common and gives decent messages
- **Note:** Built-in `unittest` blocks are compiled only when `-unittest` flag is passed (dub test does this automatically). Very lightweight for TDD. For more advanced runners (better filtering, parallel, xUnit style) consider `unit-threaded` or `silly` from dub.
