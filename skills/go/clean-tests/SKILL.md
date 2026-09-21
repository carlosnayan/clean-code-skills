---
name: clean-tests
description: Use when writing, fixing, editing, or refactoring Go tests. Enforces Clean Code principles—fast tests, boundary coverage, one concept per test.
when_to_use: |
  Also trigger on: slow or flaky tests, `t.Skip` without a clear reason, tests that only cover the happy path, tests asserting several unrelated concepts, missing boundary cases (empty slices, off-by-one, page zero), missing `t.Parallel()` on independent tests, table-driven tests without subtest names, or asks about "coverage gap" / "edge case".
---

# Clean Tests

## T1: Insufficient Tests

Test everything that could possibly break. Use coverage tools as a guide, not a goal.

```go
// Bad - only tests the happy path
func TestDivide(t *testing.T) {
	got, err := Divide(10, 2)
	if err != nil || got != 5 {
		t.Fatalf("Divide(10, 2) = %v, %v; want 5, nil", got, err)
	}
}

// Good - table-driven, covers the edges
func TestDivide(t *testing.T) {
	tests := []struct {
		name    string
		a, b    float64
		want    float64
		wantErr error
	}{
		{name: "normal", a: 10, b: 2, want: 5},
		{name: "negative", a: -10, b: 2, want: -5},
		{name: "by zero", a: 10, b: 0, wantErr: ErrDivideByZero},
	}

	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			t.Parallel()

			got, err := Divide(tt.a, tt.b)
			if !errors.Is(err, tt.wantErr) {
				t.Fatalf("Divide(%v, %v) error = %v; want %v", tt.a, tt.b, err, tt.wantErr)
			}
			if err == nil && got != tt.want {
				t.Errorf("Divide(%v, %v) = %v; want %v", tt.a, tt.b, got, tt.want)
			}
		})
	}
}
```

## T2: Use a Coverage Tool

Coverage tools report gaps in your testing strategy. Don't ignore them.

```bash
go test ./... -cover
go test ./... -coverprofile=cover.out && go tool cover -html=cover.out

# Aim for meaningful coverage, not 100%
```

Run `go test -race ./...` too: in Go, a missing race detector run is a coverage gap.

## T3: Don't Skip Trivial Tests

Trivial tests document behavior and catch regressions. They're worth more than their cost.

```go
// Worth having - documents expected behavior
func TestNewUserDefaultsToMember(t *testing.T) {
	user := NewUser("Alice")
	if user.Role != RoleMember {
		t.Errorf("NewUser(%q).Role = %v; want %v", "Alice", user.Role, RoleMember)
	}
}
```

## T4: An Ignored Test Is a Question About an Ambiguity

Don't use `t.Skip` to hide problems. Either fix the test or delete it.

```go
// Bad - hiding a problem
func TestAsyncOperation(t *testing.T) {
	t.Skip("flaky, fix later")
}

// Good - either fix it, or say exactly what the skip depends on
func TestCacheInvalidation(t *testing.T) {
	if testing.Short() {
		t.Skip("requires Redis; see CONTRIBUTING.md")
	}
	// ...
}
```

## T5: Test Boundary Conditions

Bugs congregate at boundaries. Test them explicitly.

```go
func TestPaginateBoundaries(t *testing.T) {
	items := make([]int, 100)

	got, err := Paginate(items, 1, 10) // first page
	requireEqual(t, items[:10], got, err)

	got, err = Paginate(items, 10, 10) // last page
	requireEqual(t, items[90:], got, err)

	got, err = Paginate(items, 11, 10) // beyond last page
	requireEqual(t, []int{}, got, err)

	if _, err := Paginate(items, 0, 10); !errors.Is(err, ErrInvalidPage) { // page zero
		t.Errorf("Paginate(page 0) error = %v; want ErrInvalidPage", err)
	}

	got, err = Paginate(nil, 1, 10) // empty input
	requireEqual(t, []int{}, got, err)
}
```

## T6: Exhaustively Test Near Bugs

When you find a bug, write tests for all similar cases. Bugs cluster.

```go
// Found bug: off-by-one in date calculation.
// Now test ALL date boundaries.
func TestLastDayOfMonth(t *testing.T) {
	tests := []struct {
		year, month, want int
	}{
		{2024, 1, 31},  // January
		{2024, 2, 29},  // leap-year February
		{2023, 2, 28},  // non-leap February
		{2024, 4, 30},  // 30-day month
		{2024, 12, 31}, // December
	}
	// ...
}
```

## T7: Patterns of Failure Are Revealing

When tests fail, look for patterns. They often point to deeper issues.

```go
// If every goroutine test fails intermittently under -race,
// the problem isn't the tests—it's the synchronization.
```

## T8: Test Coverage Patterns Can Be Revealing

Look at which code paths are untested. Often they reveal design problems.

```go
// If a function needs five fakes to test, it probably does too much.
// Refactor for testability: accept small interfaces, return concrete values.
```

## T9: Tests Should Be Fast

Slow tests don't get run. Keep unit tests under 100ms each, and mark the slow ones.

```go
// Bad - hits a real database
func TestCreateUser(t *testing.T) {
	db, err := sql.Open("postgres", os.Getenv("DATABASE_URL")) // Slow!
	// ...
}

// Good - in-memory fake
func TestCreateUser(t *testing.T) {
	t.Parallel()

	users := NewInMemoryUserStore()
	user, err := users.Create(context.Background(), "Alice")
	if err != nil {
		t.Fatalf("Create() error = %v", err)
	}
	if user.Name != "Alice" {
		t.Errorf("Create().Name = %q; want %q", user.Name, "Alice")
	}
}
```

Gate genuinely slow tests behind `testing.Short()` and run `go test -short ./...` locally.

## Test Organization

### F.I.R.S.T. Principles

- **Fast**: Tests should run quickly
- **Independent**: Tests shouldn't depend on each other—`t.Parallel()` proves it
- **Repeatable**: Same result every time, any environment
- **Self-Validating**: Pass or fail, no manual inspection
- **Timely**: Written before or with the code, not after

### One Concept Per Test

```go
// Bad - testing multiple things
func TestUser(t *testing.T) {
	user := NewUser("Alice", "alice@example.com")
	if user.Name != "Alice" { t.Error("wrong name") }
	if user.Email != "alice@example.com" { t.Error("wrong email") }
	if !user.IsValid() { t.Error("should be valid") }
	user.Activate()
	if !user.IsActive { t.Error("should be active") }
}

// Good - one concept each
func TestNewUserStoresName(t *testing.T)  { ... }
func TestNewUserStoresEmail(t *testing.T) { ... }
func TestNewUserIsValid(t *testing.T)     { ... }
func TestUserActivate(t *testing.T)       { ... }
```

### Go Conventions

- Use `t.Helper()` in assertion helpers so failures point at the caller.
- Use `t.Cleanup()` instead of manual teardown; it runs even on failure.
- `t.Fatal` when the test cannot continue, `t.Error` when it can.
- Write failure messages as `got; want`: `t.Errorf("Sum() = %d; want %d", got, want)`.
- Keep table-driven tests named so `go test -run TestX/case_name` works.

## Quick Reference

| Rule | Principle |
|------|-----------|
| T1 | Test everything that could break |
| T2 | Use coverage tools (and `-race`) |
| T3 | Don't skip trivial tests |
| T4 | Ignored test = ambiguity question |
| T5 | Test boundary conditions |
| T6 | Exhaustively test near bugs |
| T7 | Look for patterns in failures |
| T8 | Check coverage when debugging |
| T9 | Tests must be fast (<100ms) |
