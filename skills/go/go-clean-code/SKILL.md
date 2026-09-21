---
name: go-clean-code
description: Use when writing, fixing, editing, reviewing, or refactoring any Go code. Enforces Robert Martin's complete Clean Code catalog—naming, functions, comments, DRY, and boundary conditions.
---

# Clean Go: Complete Reference

Enforces all Clean Code principles from Robert C. Martin's Chapter 17, adapted for Go.

## Comments (C1-C5)
- C1: No metadata in comments (use Git)
- C2: Delete obsolete comments immediately
- C3: No redundant comments
- C4: Write comments well; doc comments start with the identifier name
- C5: Never commit commented-out code

## Environment (E1-E2)
- E1: One command to build (`go build ./...`)
- E2: One command to test (`go test ./...`)

## Functions (F1-F6)
- F1: Maximum 3 arguments (use a struct for more; leading `ctx` doesn't count)
- F2: No output arguments (return values)
- F3: No flag arguments (split functions)
- F4: Delete dead functions
- F5: Never ignore a returned error
- F6: Return early; keep the happy path at the left margin

## General (G1-G36)
- G1: One language per file
- G2: Implement expected behavior
- G3: Handle boundary conditions
- G4: Don't override safeties
- G5: DRY - no duplication
- G6: Consistent abstraction levels
- G7: Embedded types don't know their embedders
- G8: Minimize the exported surface
- G9: Delete dead code
- G10: Variables near usage
- G11: Be consistent
- G12: Remove clutter
- G13: No artificial coupling
- G14: No feature envy
- G15: No selector arguments
- G16: No obscured intent
- G17: Code where expected
- G18: Prefer methods on the type that owns the data
- G19: Use explanatory variables
- G20: Function names say what they do
- G21: Understand the algorithm
- G22: Make dependencies physical
- G23: Prefer interfaces to type switches
- G24: Follow conventions (`gofmt`, Effective Go, `go vet`, golangci-lint)
- G25: Named constants, not magic numbers
- G26: Be precise
- G27: Structure over convention
- G28: Encapsulate conditionals
- G29: Avoid negative conditionals
- G30: Functions do one thing
- G31: Make temporal coupling explicit
- G32: Don't be arbitrary
- G33: Encapsulate boundary conditions
- G34: One abstraction level per function
- G35: Config at high levels
- G36: Law of Demeter (no train wrecks)

## Go-Specific (GO1-GO5)
These adapt the Java-specific rules (J1-J3) to Go conventions:
- GO1: Keep imports explicit and stable; no dot-imports, no `init()` side effects for setup the caller should own
- GO2: Use typed constants with `iota`, not magic strings or bare ints
- GO3: Accept interfaces, return structs; keep interfaces small and defined by the consumer, and avoid `any` at package boundaries
- GO4: Errors are values—wrap with `%w`, compare with `errors.Is`/`errors.As`, reserve `panic` for unrecoverable programmer errors
- GO5: Every goroutine needs a defined exit—a `context.Context`, a closed channel, or a `WaitGroup` the caller waits on

## Names (N1-N7)
- N1: Choose descriptive names
- N2: Right abstraction level
- N3: Use standard nomenclature (`-er` interfaces, no `Get` prefix)
- N4: Unambiguous names
- N5: Name length matches scope
- N6: No encodings, no `I` prefix, no stuttering, `MixedCaps` only
- N7: Names describe side effects

## Tests (T1-T9)
- T1: Test everything that could break
- T2: Use coverage tools (and `-race`)
- T3: Don't skip trivial tests
- T4: Ignored test = ambiguity question
- T5: Test boundary conditions
- T6: Exhaustively test near bugs
- T7: Look for patterns in failures
- T8: Check coverage when debugging
- T9: Tests must be fast (< 100ms each)

## Quick Reference Table

| Category | Rule | One-Liner |
|----------|------|-----------|
| **Comments** | C1 | No metadata (use Git) |
| | C3 | No redundant comments |
| | C5 | No commented-out code |
| **Functions** | F1 | Max 3 arguments |
| | F3 | No flag arguments |
| | F4 | Delete dead functions |
| | F5 | Never ignore an error |
| **General** | G5 | DRY—no duplication |
| | G9 | Delete dead code |
| | G16 | No obscured intent |
| | G23 | Interfaces over type switches |
| | G25 | Named constants, not magic numbers |
| | G30 | Functions do one thing |
| | G36 | Law of Demeter (one dot) |
| **Go** | GO3 | Accept interfaces, return structs |
| | GO4 | Wrap errors with `%w`, don't panic |
| | GO5 | Every goroutine has an exit |
| **Names** | N1 | Descriptive names |
| | N5 | Name length matches scope |
| **Tests** | T5 | Test boundary conditions |
| | T9 | Tests must be fast |

## Anti-Patterns (Don't → Do)

| ❌ Don't | ✅ Do |
|----------|-------|
| Comment every line | Delete obvious comments |
| Helper for a one-liner | Inline the code |
| `import . "pkg"` | Named imports for explicit dependencies |
| `any` / `interface{}` in the exported API | Concrete types or generics |
| Magic number `86400` | `const secondsPerDay = 86400` or `24*time.Hour` |
| `Process(data, true)` | `ProcessVerbose(data)` |
| `_ = doThing()` | `if err := doThing(); err != nil { ... }` |
| `panic(err)` in a library | Return the error to the caller |
| `return errors.New("failed")` | `fmt.Errorf("loading %s: %w", id, err)` |
| Deep nesting | Guard clauses, early returns |
| `obj.A.B.C.Value` | `obj.Value()` |
| `package utils` | A package named for what it does |
| 100+ line function | Split by responsibility |

## AI Behavior

When reviewing code, identify violations by rule number (e.g., "G5 violation: duplicated logic").
When fixing or editing code, report what was fixed (e.g., "Fixed: extracted magic number to `secondsPerDay` (G25)").
