---
name: clean-general
description: Use when writing, fixing, editing, or reviewing Go code quality. Enforces Clean Code's core principles—DRY, single responsibility, clear intent, no magic numbers, proper abstractions.
when_to_use: |
  Also trigger on: duplicated logic across files or branches (G5), magic numbers or hardcoded strings (G25), long if/else or type-switch chains that should be an interface (G23), chained field access like `a.B.C.D` (G36), functions juggling multiple responsibilities (G30), clever one-liners whose intent is not obvious (G16), or panics used for ordinary error paths.
---

# General Clean Code Principles

## Critical Rules

**G5: DRY (Don't Repeat Yourself)**

Every piece of knowledge has one authoritative representation.

```go
// Bad - duplication
const taxRate = 0.0825
caTotal := subtotal * 1.0825
nyTotal := subtotal * 1.07

// Good - single source of truth
var taxRates = map[string]float64{"CA": 0.0825, "NY": 0.07}

func CalculateTotal(subtotal float64, state string) (float64, error) {
	rate, ok := taxRates[state]
	if !ok {
		return 0, fmt.Errorf("no tax rate for state %q", state)
	}
	return subtotal * (1 + rate), nil
}
```

**G16: No Obscured Intent**

Don't be clever. Be clear.

```go
// Bad - what does this do?
return ((x & 0x0f) << 4) | (y & 0x0f)

// Good - obvious intent
return packCoordinates(x, y)
```

**G23: Prefer Polymorphism to If/Else**

In Go, that means an interface instead of a growing switch on a type field.

```go
// Bad - will grow forever
type Employee struct {
	Type       string
	Salary     float64
	Hours      float64
	Rate       float64
	Base       float64
	Commission float64
}

func CalculatePay(e Employee) float64 {
	switch e.Type {
	case "SALARIED":
		return e.Salary
	case "HOURLY":
		return e.Hours * e.Rate
	case "COMMISSIONED":
		return e.Base + e.Commission
	}
	return 0
}

// Good - open/closed principle
type Employee interface {
	CalculatePay() float64
}

type SalariedEmployee struct{ Salary float64 }

func (e SalariedEmployee) CalculatePay() float64 { return e.Salary }

type HourlyEmployee struct {
	Hours float64
	Rate  float64
}

func (e HourlyEmployee) CalculatePay() float64 { return e.Hours * e.Rate }

type CommissionedEmployee struct {
	Base       float64
	Commission float64
}

func (e CommissionedEmployee) CalculatePay() float64 { return e.Base + e.Commission }
```

Define the interface in the package that *consumes* it, not the one that implements it.

**G25: Replace Magic Numbers with Named Constants**

```go
// Bad
if elapsed > 86400 {
	// ...
}

// Good
const secondsPerDay = 86400
if elapsed > secondsPerDay {
	// ...
}

// Better, when it's a duration
if elapsed > 24*time.Hour {
	// ...
}
```

**G30: Functions Should Do One Thing**

If you can extract another function, your function does more than one thing.

**G36: Law of Demeter (Avoid Train Wrecks)**

```go
// Bad - reaching through multiple structs
outputDir := ctx.Options.ScratchDir.AbsolutePath

// Good - one dot
outputDir := ctx.ScratchDir()
```

## Go-Specific Additions

**Errors are values, not exceptions.** Reserve `panic` for truly unrecoverable
programmer errors; never use it for control flow across package boundaries.
Wrap with `%w` so callers can use `errors.Is` / `errors.As`, and don't log and
return the same error.

**Don't start a goroutine without knowing how it stops.** Every goroutine needs a
clear exit: a closed channel, a cancelled `context.Context`, or a `sync.WaitGroup`
the caller waits on.

**Accept interfaces, return structs.** Keep parameter interfaces small—one or two
methods—and return concrete types so callers keep full information.

## Enforcement Checklist

When reviewing AI-generated code, verify:
- [ ] No duplication (G5)
- [ ] Clear intent, no magic numbers (G16, G25)
- [ ] Interfaces over type switches (G23)
- [ ] Functions do one thing (G30)
- [ ] No Law of Demeter violations (G36)
- [ ] Boundary conditions handled (G3)
- [ ] Dead code removed (G9)
- [ ] Every error handled or wrapped, no stray `panic`
- [ ] `gofmt`, `go vet ./...` clean (G24)
