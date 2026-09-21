---
name: clean-comments
description: Use when writing, fixing, editing, or reviewing Go comments and doc comments. Enforces Clean Code principles—no metadata, no redundancy, no commented-out code.
when_to_use: |
  Also trigger on: commented-out code blocks, TODO/FIXME banners, author/ticket/date metadata in comments, doc comments that no longer match the code, doc comments that don't start with the identifier name, redundant comments that restate the code (e.g. `i++ // increment i`), or asks like "is this comment useful", "why is this block commented".
---

# Clean Comments

## C1: No Inappropriate Information

Comments shouldn't hold metadata. Use Git for author names, change history,
ticket numbers, and dates. Comments are for technical notes about code only.

```go
// Bad - metadata belongs in Git
// Author: alice@example.com
// Modified: 2024-03-11, ticket PROJ-482
func ParseConfig(path string) (Config, error) { ... }
```

## C2: Delete Obsolete Comments

If a comment describes code that no longer exists or works differently,
delete it immediately. Stale comments become "floating islands of
irrelevance and misdirection."

## C3: No Redundant Comments

```go
// Bad - the code already says this
i++       // increment i
user.Save() // save the user

// Good - explains WHY, not WHAT
i++ // compensate for zero-indexing in display
```

## C4: Write Comments Well

If a comment is worth writing, write it well:
- Start doc comments with the identifier name (`// ParseConfig reads...`)
- Write complete sentences
- Don't ramble or state the obvious
- Be brief

```go
// Bad - doesn't follow godoc convention, and says nothing
// this function parses
func ParseConfig(path string) (Config, error) { ... }

// Good
// ParseConfig reads the TOML file at path and returns the decoded Config.
// It returns ErrMissingFile if path does not exist.
func ParseConfig(path string) (Config, error) { ... }
```

Every exported identifier deserves a doc comment; unexported helpers usually don't.

## C5: Never Commit Commented-Out Code

```go
// DELETE THIS - it's an abomination
// func oldCalculateTax(income float64) float64 {
// 	return income * 0.15
// }
```

Who knows how old it is? Who knows if it's meaningful? Delete it.
Git remembers everything.

## The Goal

The best comment is the code itself. If you need a comment to explain
what code does, refactor first, comment last.
