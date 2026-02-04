# Contributing to lazytrack

Thanks for your interest in contributing to lazytrack.

lazytrack is a git-native, plain-text project tracker with a terminal UI. The design priorities are simplicity, correctness, keyboard-first UX, and user data ownership.

Please read this document before submitting changes.

---

# Project Goals

- Git-native workflow
- Plain text storage
- No lock-in
- Small, focused feature set
- Terminal-first usability
- Automation via git + PR metadata
- Low operational complexity

If a feature increases system complexity without strong workflow payoff, it will likely be rejected.

---

# Before Opening an Issue

Please check:

- existing issues
- roadmap section in README
- whether the feature fits core scope

Good issues include:

- reproducible bugs
- UX friction reports
- git workflow edge cases
- merge/conflict safety issues
- performance problems

---

# Before Submitting a PR

1. Open an issue if the change is non-trivial
2. Describe the workflow problem being solved
3. Keep PRs focused and small
4. Include tests where appropriate
5. Update docs when behavior changes

---

# Code Guidelines

## General

- Prefer clarity over cleverness
- Avoid hidden magic
- Avoid global state where possible
- Fail loudly, not silently
- Deterministic behavior over heuristics

## CLI / TUI

- Keyboard-first interactions
- Consistent keybindings
- Visible state transitions
- No destructive action without confirmation

## Storage Layer

- Never silently rewrite user files
- Preserve formatting when possible
- Be merge-friendly
- Be diff-friendly

---

# Commit Message Style

Use structured messages when possible:

```
area: short summary

optional longer explanation
```

Examples:

```
parser: fix frontmatter multiline bug
tui: add task filter panel
git: improve commit link detection
```

---

# Testing Focus

High-value test areas:

- task file parsing
- round-trip serialization
- merge conflict safety
- automation rule execution
- git metadata linking

---

# Philosophy Check

Before merging a feature, ask:

Does this make git-based project tracking faster, safer, or clearer?

If not — it probably doesn’t belong.
