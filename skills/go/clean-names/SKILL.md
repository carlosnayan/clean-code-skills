---
name: clean-names
description: Use when naming, renaming, or fixing names of variables, functions, types, interfaces, or packages in Go. Enforces Clean Code principles—descriptive names, appropriate length, no encodings.
when_to_use: |
  Also trigger on: cryptic identifiers (`d`, `x`, `proc`), Hungarian notation (`strName`, `arrUsers`, `nCount`), `snake_case` identifiers, `I`-prefixed interfaces (`IUserRepository`), stuttering names (`user.UserService`), `Get` prefixes on plain accessors, function names that hide side effects (e.g. `Config` that also writes a file), ambiguous names like `Rename(source, target)`, or asks like "rename this", "clearer name".
---

# Clean Names

## N1: Choose Descriptive Names

Names should reveal intent. If a name requires a comment, it doesn't reveal its intent.

```go
// Bad - what is d?
const d = 86400

// Good - obvious meaning
const secondsPerDay = 86400

// Bad - what does this function do?
func proc(values []int) []int { ... }

// Good - intent is clear
func FilterPositive(numbers []int) []int { ... }
```

## N2: Choose Names at the Appropriate Level of Abstraction

Don't pick names that communicate implementation; choose names that reflect the level of abstraction of the type or function.

```go
// Bad - too implementation-specific
func GetMapOfUserIDsToNames() map[string]string { ... }

// Good - abstracts the data structure
func UserDirectory() map[string]string { ... }
```

Go convention: drop the `Get` prefix on accessors. `user.Name()`, not `user.GetName()`.

## N3: Use Standard Nomenclature Where Possible

Use terms from the domain, design patterns, or well-known Go conventions.

```go
// Good - uses pattern name
type UserFactory struct{}

func (f UserFactory) Create(data UserData) (User, error) { ... }

// Good - single-method interfaces end in -er
type Notifier interface {
	Notify(ctx context.Context, userID string) error
}

// Good - uses domain term
func CalculateAmortization(principal, rate float64, termMonths int) float64 { ... }
```

## N4: Unambiguous Names

Choose names that make the workings of a function or variable unambiguous.

```go
// Bad - ambiguous
func Rename(source, target string) error { ... }

// Good - clear what's being renamed
func RenameFile(oldPath, newPath string) error { ... }
```

## N5: Use Longer Names for Longer Scopes

Go leans hard on this rule: short names for tiny scopes, longer names as scope grows.

```go
// Good - short name for a tiny scope
for i, u := range users { ... }

// Good - short receiver name, as is idiomatic
func (s *Server) Shutdown(ctx context.Context) error { ... }

// Good - longer name for a package-level constant
const maxRetryAttemptsBeforeFailure = 5

// Bad - short name at package level
const max = 5
```

## N6: Avoid Encodings

Don't encode type or scope information into names.

```go
// Bad - Hungarian notation
strName := "Alice"
arrUsers := []string{}
nCount := 0

// Good - clean names
name := "Alice"
users := []string{}
count := 0

// Bad - interface prefix, not a Go convention
type IUserRepository interface {
	FindByID(ctx context.Context, id string) (User, error)
}

// Good - just name it
type UserRepository interface {
	FindByID(ctx context.Context, id string) (User, error)
}
```

Also avoid stuttering: in package `user`, the type is `user.Service`, not `user.UserService`.
Use `MixedCaps`, never `snake_case`. Initialisms stay uppercase: `userID`, `ServeHTTP`, `parseURL`.

## N7: Names Should Describe Side Effects

If a function does something beyond what its name suggests, the name is misleading.

```go
// Bad - name doesn't mention file creation
func Config(path string) (Config, error) {
	if _, err := os.Stat(path); os.IsNotExist(err) {
		os.WriteFile(path, []byte("{}"), 0o644) // Hidden side effect!
	}
	return load(path)
}

// Good - name reveals behavior
func LoadOrCreateConfig(path string) (Config, error) {
	if _, err := os.Stat(path); os.IsNotExist(err) {
		if err := os.WriteFile(path, []byte("{}"), 0o644); err != nil {
			return Config{}, fmt.Errorf("creating %s: %w", path, err)
		}
	}
	return load(path)
}
```

## Package Names

Package names are part of every identifier they qualify. Keep them short, lowercase,
singular, and free of underscores or plurals: `http`, `user`, `billing`—never
`utils`, `helpers`, `common`, or `base`.

## Quick Reference

| Rule | Principle | Example |
|------|-----------|---------|
| N1 | Descriptive names | `secondsPerDay` not `d` |
| N2 | Right abstraction level | `UserDirectory()` not `GetMapOf...` |
| N3 | Standard nomenclature | `UserFactory`, `Notifier` |
| N4 | Unambiguous | `RenameFile(oldPath, newPath)` |
| N5 | Length matches scope | `u` in a loop, long for package level |
| N6 | No encodings | `users` not `arrUsers`, no `I` prefix |
| N7 | Describe side effects | `LoadOrCreateConfig()` |
