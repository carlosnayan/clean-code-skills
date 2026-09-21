# Clean Code Skills for AI Agents

[![Agent Skills](https://img.shields.io/badge/Agent%20Skills-Compatible-blue)](https://agentskills.io)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

**Teach your AI to write code that doesn't suck.**

This repository contains [Agent Skills](https://agentskills.io) that enforce Robert C. Martin's _Clean Code_ principles. They work with Google Antigravity, Anthropic's Claude Code, and any agent that supports the Agent Skills standard.

## Why?

AI generates code fast, but research shows it also generates technical debt fast:

- **GitClear**: 4x increase in code duplication with AI adoption
- **Carnegie Mellon**: +30% static analysis warnings, +41% code complexity after Cursor adoption
- **Google DORA**: Negative relationship between AI adoption and software delivery stability

These skills encode battle-tested solutions to exactly these problems—directly into your AI workflow.

## What's Included

| Track      | Skill                   | Description                                                  | Rules                                              |
| ---------- | ----------------------- | ------------------------------------------------------------ | -------------------------------------------------- |
| Go         | `boy-scout`             | **Orchestrator**—always leave code cleaner than you found it | Coordinates all skills                             |
| Go         | `go-clean-code`         | **Master skill** with all 70 rules                           | C1-C5, E1-E2, F1-F6, G1-G36, GO1-GO5, N1-N7, T1-T9 |
| Go         | `clean-comments`        | Minimal, accurate commenting                                 | C1-C5                                              |
| Go         | `clean-functions`       | Small, focused, obvious functions                            | F1-F6                                              |
| Go         | `clean-general`         | Core principles (DRY, single responsibility)                 | G5, G16, G23, G25, G30, G36                        |
| Go         | `clean-names`           | Descriptive, unambiguous naming                              | N1-N7                                              |
| Go         | `clean-tests`           | Fast, thorough, boundary-aware tests                         | T1-T9                                              |
| TypeScript | `boy-scout`             | **Orchestrator**—always leave code cleaner than you found it | Coordinates all skills                             |
| TypeScript | `typescript-clean-code` | **Master skill** with all 66 rules                           | C1-C5, E1-E2, F1-F4, G1-G36, N1-N7, TS1-TS3, T1-T9 |
| TypeScript | `clean-comments`        | Minimal, accurate commenting                                 | C1-C5                                              |
| TypeScript | `clean-functions`       | Small, focused, obvious functions                            | F1-F4                                              |
| TypeScript | `clean-general`         | Core principles (DRY, single responsibility)                 | G5, G16, G23, G25, G30, G36                        |
| TypeScript | `clean-names`           | Descriptive, unambiguous naming                              | N1-N7                                              |
| TypeScript | `clean-tests`           | Fast, thorough, boundary-aware tests                         | T1-T9                                              |

Use the master skill for comprehensive coverage, or individual skills for targeted enforcement.

### Choose Your Language

Pick one track and copy only that track's skills:

> [!WARNING]
> Install only one language track per skills directory. Go and TypeScript tracks reuse the same skill names (`boy-scout`, `clean-functions`, etc.). Installing both together can make the agent load conflicting instructions and behave inconsistently.

```bash
# Go track
cp -r skills/go/* <YOUR_SKILLS_DIR>/

# TypeScript track
cp -r skills/typescript/* <YOUR_SKILLS_DIR>/
```

### The Boy Scout Rule

The `boy-scout` skill embodies Clean Code's core philosophy:

> "Always check a module in cleaner than when you checked it out."

You don't have to make code perfect—just **a little bit better** with every touch. The `boy-scout` skill orchestrates the others, ensuring every code interaction leaves a trail of small improvements.

---

## Installation

Install only one language track per destination directory (`.agent/skills`, `.claude/skills`, `~/.claude/skills`, etc.).

### Google Antigravity

**Project-specific** (applies to one project):

```bash
# From your project root
mkdir -p .agent/skills
# Go track
cp -r skills/go/* .agent/skills/

# TypeScript track
cp -r skills/typescript/* .agent/skills/
```

**Global** (applies to all projects):

```bash
mkdir -p ~/.gemini/antigravity/skills
# Go track
cp -r skills/go/* ~/.gemini/antigravity/skills/

# TypeScript track
cp -r skills/typescript/* ~/.gemini/antigravity/skills/
```

**Quick install** (global, one command) — pick one track:

```bash
# Go track
git clone https://github.com/carlosnayan/clean-code-skills.git /tmp/clean-code-skills && \
mkdir -p ~/.gemini/antigravity/skills && \
cp -r /tmp/clean-code-skills/skills/go/* ~/.gemini/antigravity/skills/ && \
rm -rf /tmp/clean-code-skills
```

```bash
# TypeScript track
git clone https://github.com/carlosnayan/clean-code-skills.git /tmp/clean-code-skills && \
mkdir -p ~/.gemini/antigravity/skills && \
cp -r /tmp/clean-code-skills/skills/typescript/* ~/.gemini/antigravity/skills/ && \
rm -rf /tmp/clean-code-skills
```

### Anthropic Claude Code

**Project-specific**:

```bash
# From your project root
mkdir -p .claude/skills
# Go track
cp -r skills/go/* .claude/skills/

# TypeScript track
cp -r skills/typescript/* .claude/skills/
```

**Global**:

```bash
mkdir -p ~/.claude/skills
# Go track
cp -r skills/go/* ~/.claude/skills/

# TypeScript track
cp -r skills/typescript/* ~/.claude/skills/
```

**Quick install** (global, one command) — pick one track:

```bash
# Go track
git clone https://github.com/carlosnayan/clean-code-skills.git /tmp/clean-code-skills && \
mkdir -p ~/.claude/skills && \
cp -r /tmp/clean-code-skills/skills/go/* ~/.claude/skills/ && \
rm -rf /tmp/clean-code-skills
```

```bash
# TypeScript track
git clone https://github.com/carlosnayan/clean-code-skills.git /tmp/clean-code-skills && \
mkdir -p ~/.claude/skills && \
cp -r /tmp/clean-code-skills/skills/typescript/* ~/.claude/skills/ && \
rm -rf /tmp/clean-code-skills
```

**Verify**

In a running Claude Code session, confirm the skills loaded:

- Ask `What skills are available?` — you should see `boy-scout`, `clean-comments`, `clean-functions`, `clean-general`, `clean-names`, `clean-tests`, and `go-clean-code` in the list.
- Or direct-invoke one: `/boy-scout` should load the Boy Scout skill explicitly.

Skills hot-reload inside an existing `~/.claude/skills/` directory — no restart needed. If you created the directory for the first time during this session, restart Claude Code once so it starts watching it.

**Update**

Re-run the Quick install command to pull the latest version. It overwrites the seven skill directories and leaves any other skills untouched.

If you expect to update often, symlink instead of copy:

```bash
git clone https://github.com/carlosnayan/clean-code-skills.git ~/src/clean-code-skills
# Pick one track — swap `go` for `typescript` to use the TS track.
cd ~/src/clean-code-skills/skills/go
for d in */; do ln -sfn "$PWD/${d%/}" "$HOME/.claude/skills/${d%/}"; done
```

Then `git pull` in `~/src/clean-code-skills` refreshes every skill.

**Uninstall**

```bash
rm -rf ~/.claude/skills/{boy-scout,clean-comments,clean-functions,clean-general,clean-names,clean-tests,go-clean-code}
```

### Other Agent Skills-Compatible Tools

The skills follow the [Agent Skills](https://agentskills.io) open standard. Check your tool's documentation for the skills directory location, then copy the `skills/` folder contents there.

---

## Usage

Once installed, skills activate automatically based on context. Ask your agent to:

- **Write code**: "Create a user authentication module" → Skills enforce clean patterns
- **Review code**: "Review this function for issues" → Agent identifies violations by rule number
- **Refactor**: "Refactor this to be cleaner" → Agent applies all relevant rules

### Example

**Before** (10 violations):

```go
// Author: John, Modified: 2024-01-15  // C1
func proc(d []map[string]any, t string, flag bool) []float64 { // N1, F1, F3, GO3
	// Process the data  // C3
	x := []float64{} // N1
	for _, i := range d {
		if flag { // F3
			if i["type"] == "A" { // G23
				x = append(x, i["val"].(float64)*1.0825) // G25
			} else if i["type"] == "B" {
				x = append(x, i["val"].(float64)*1.05) // G25
			}
		} else {
			x = append(x, i["val"].(float64))
		}
	}
	return x
}
```

**After (Go track)**:

```go
const (
	taxRateCA = 0.0825
	taxRateNY = 0.05
)

type State string

const (
	StateCA State = "CA"
	StateNY State = "NY"
)

type Transaction struct {
	Value float64
	State State
}

var taxRates = map[State]float64{StateCA: taxRateCA, StateNY: taxRateNY}

// ApplyTax returns the transaction value with its state tax applied.
func ApplyTax(transaction Transaction) (float64, error) {
	rate, ok := taxRates[transaction.State]
	if !ok {
		return 0, fmt.Errorf("no tax rate for state %q", transaction.State)
	}
	return transaction.Value * (1 + rate), nil
}

// TaxedValues returns the taxed value of every transaction.
func TaxedValues(transactions []Transaction) ([]float64, error) {
	values := make([]float64, 0, len(transactions))
	for _, transaction := range transactions {
		value, err := ApplyTax(transaction)
		if err != nil {
			return nil, err
		}
		values = append(values, value)
	}
	return values, nil
}

// RawValues returns the untaxed value of every transaction.
func RawValues(transactions []Transaction) []float64 {
	values := make([]float64, 0, len(transactions))
	for _, transaction := range transactions {
		values = append(values, transaction.Value)
	}
	return values
}
```

**After (TypeScript track)**:

```ts
type TransactionType = "CA" | "NY";

const TAX_RATE_CA = 0.0825;
const TAX_RATE_NY = 0.05;

type Transaction = {
  value: number;
  type: TransactionType;
};

function applyTax(transaction: Transaction): number {
  const taxRates: Record<TransactionType, number> = {
    CA: TAX_RATE_CA,
    NY: TAX_RATE_NY,
  };
  return transaction.value * (1 + taxRates[transaction.type]);
}

function processTransactionsWithTax(transactions: Transaction[]): number[] {
  return transactions.map(applyTax);
}

function processTransactionsWithoutTax(transactions: Transaction[]): number[] {
  return transactions.map((transaction) => transaction.value);
}
```

---

## Rule Reference

### Comments (C1-C5)

| Rule | Principle                            |
| ---- | ------------------------------------ |
| C1   | No metadata in comments (use Git)    |
| C2   | Delete obsolete comments immediately |
| C3   | No redundant comments                |
| C4   | Write comments well if you must      |
| C5   | Never commit commented-out code      |

### Functions (F1-F6)

| Rule | Principle                                               |
| ---- | ------------------------------------------------------- |
| F1   | Maximum 3 arguments                                     |
| F2   | No output arguments                                     |
| F3   | No flag arguments                                       |
| F4   | Delete dead functions                                   |
| F5   | Never ignore a returned error _(Go track)_              |
| F6   | Return early, keep the happy path unnested _(Go track)_ |

### General (G1-G36)

| Rule | Principle                                                    |
| ---- | ------------------------------------------------------------ |
| G1   | One language per file                                        |
| G2   | Implement expected behavior                                  |
| G3   | Handle boundary conditions                                   |
| G4   | Don't override safeties                                      |
| G5   | DRY—no duplication                                           |
| G6   | Consistent abstraction levels                                |
| G7   | Base classes don't know children                             |
| G8   | Minimize public interface                                    |
| G9   | Delete dead code                                             |
| G10  | Variables near usage                                         |
| G11  | Be consistent                                                |
| G12  | Remove clutter                                               |
| G13  | No artificial coupling                                       |
| G14  | No feature envy                                              |
| G15  | No selector arguments                                        |
| G16  | No obscured intent                                           |
| G17  | Code where expected                                          |
| G18  | Prefer instance methods                                      |
| G19  | Use explanatory variables                                    |
| G20  | Function names say what they do                              |
| G21  | Understand the algorithm                                     |
| G22  | Make dependencies physical                                   |
| G23  | Polymorphism over if/else                                    |
| G24  | Follow conventions (language style guide + linter/formatter) |
| G25  | Named constants, not magic numbers                           |
| G26  | Be precise                                                   |
| G27  | Structure over convention                                    |
| G28  | Encapsulate conditionals                                     |
| G29  | Avoid negative conditionals                                  |
| G30  | Functions do one thing                                       |
| G31  | Make temporal coupling explicit                              |
| G32  | Don't be arbitrary                                           |
| G33  | Encapsulate boundary conditions                              |
| G34  | One abstraction level per function                           |
| G35  | Config at high levels                                        |
| G36  | Law of Demeter (one dot)                                     |

### Names (N1-N7)

| Rule | Principle                              |
| ---- | -------------------------------------- |
| N1   | Choose descriptive names               |
| N2   | Names at appropriate abstraction level |
| N3   | Use standard nomenclature              |
| N4   | Unambiguous names                      |
| N5   | Name length matches scope              |
| N6   | No encodings (no Hungarian notation)   |
| N7   | Names describe side effects            |

### Go-Specific (GO1-GO5)

| Rule | Principle                                                       |
| ---- | --------------------------------------------------------------- |
| GO1  | Explicit imports; no dot-imports or `init()` setup side effects |
| GO2  | Typed constants with `iota`, not magic strings or ints          |
| GO3  | Accept interfaces, return structs; no `any` at boundaries       |
| GO4  | Errors are values—wrap with `%w`, don't `panic`                 |
| GO5  | Every goroutine has a defined exit                              |

### TypeScript-Specific (TS1-TS3)

| Rule | Principle                                            |
| ---- | ---------------------------------------------------- |
| TS1  | Keep imports explicit and stable                     |
| TS2  | Use enums or literal unions, not magic constants     |
| TS3  | Type public interfaces and avoid `any` at boundaries |

### Tests (T1-T9)

| Rule | Principle                         |
| ---- | --------------------------------- |
| T1   | Test everything that could break  |
| T2   | Use coverage tools                |
| T3   | Don't skip trivial tests          |
| T4   | Ignored test = ambiguity question |
| T5   | Test boundary conditions          |
| T6   | Exhaustively test near bugs       |
| T7   | Look for patterns in failures     |
| T8   | Check coverage when debugging     |
| T9   | Tests must be fast (<100ms)       |

---

## Customization

### Using Individual Skills

Don't need every rule? Copy only the skills you want:

```bash
# Just function rules
cp -r skills/go/clean-functions ~/.gemini/antigravity/skills/

# Just comment rules
cp -r skills/typescript/clean-comments ~/.claude/skills/
```

### Extending Skills

Add your own rules by editing the `SKILL.md` files or creating new skill folders:

```
skills/
├── go/
│   ├── go-clean-code/
│   │   └── SKILL.md
│   └── clean-comments/
│       └── SKILL.md
├── typescript/
│   ├── typescript-clean-code/
│   │   └── SKILL.md
│   └── clean-comments/
│       └── SKILL.md
└── my-team-standards/      # Your custom skill
    └── SKILL.md
```

### Adding Enforcement Scripts (Optional)

This repository does not ship a `scripts/` folder or lint scripts by default.
If you want stricter enforcement, create your own scripts inside the skill folder.

```
skills/go/go-clean-code/
├── SKILL.md
└── scripts/
    └── lint.sh
```

---

## How Skills Work

Skills use **Progressive Disclosure**:

1. **Discovery**: Agent sees only skill names and descriptions
2. **Activation**: When your request matches a description, full instructions load
3. **Execution**: Scripts and templates load only when needed

This keeps the agent fast—it's not thinking about database migrations when you're writing a React component.

---

## Contributing

PRs welcome! Some ideas:

- [ ] Keep Go/TypeScript parity as rules evolve
- [ ] Additional language support (Python, Rust)
- [ ] Integration tests
- [ ] Pre-commit hooks
- [ ] IDE extensions

---

## Resources

- [_Clean Code_](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882) by Robert C. Martin
- [Agent Skills Standard](https://agentskills.io)
- [Antigravity Documentation](https://developers.google.com/antigravity)
- [Claude Code Documentation](https://docs.anthropic.com/claude-code)

---

## License

MIT License. See [LICENSE](LICENSE) for details.

---

_The future of programming is human intent translated by AI. Make sure the translation preserves quality, not just speed._
