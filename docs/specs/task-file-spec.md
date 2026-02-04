# Task File Specification — lazytrack

Each task is stored as a single markdown file inside:

.lazytrack/tasks/

Task files must be:

- human-readable
- diff-friendly
- merge-friendly
- editable without lazytrack

---

# File Naming

LT-001.md
LT-002.md

Prefix is configurable but must be stable per repo.

---

# Structure

Task files contain:

1. Title line
2. YAML frontmatter block
3. Markdown body sections

---

# Example

# LT-014 — Add OAuth login

status: in_progress
priority: high
owner: nate
labels: [auth, backend]
milestone: v0.3
created: 2026-02-03
updated: 2026-02-05

## Description
Implement OAuth login via GitHub.

## Acceptance Criteria
- login button works
- callback handled
- session persisted

## Notes
Edge cases around token refresh.

## Links
- pr: #142

---

# Required Fields

status
created

---

# Standard Status Values

todo
in_progress
review
blocked
done

Custom statuses allowed via workflow config.

---

# Recommended Fields

priority
owner
labels
milestone
updated

---

# Rules

- Unknown fields must be preserved
- Field order should be preserved when possible
- Markdown body must not be rewritten unless edited
- Comments must not be removed

---

# Merge Safety

Writers must:

- update only changed fields
- avoid full-file rewrites
- preserve whitespace where practical
