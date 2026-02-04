# MVP Implementation Plan — lazytrack

Goal: deliver a usable git-native project tracker with minimal scope.

---

# Phase 1 — Core Storage

- task file schema
- parser + serializer
- round-trip safe writes
- ID generator
- repo detection
- config loader

Deliverable: create/edit tasks safely

---

# Phase 2 — CLI

- init command
- new task
- list tasks
- show task
- edit metadata fields
- json output mode

Deliverable: scriptable CLI usable without TUI

---

# Phase 3 — TUI

- board view
- task detail view
- status movement
- metadata editing
- search/filter

Deliverable: daily-driver TUI workflow

---

# Phase 4 — Git Integration

- commit message task detection
- branch name detection
- commit linking
- activity display

Deliverable: git-aware tasks

---

# Phase 5 — PR Integration

- gh CLI integration
- PR linking
- status automation

Deliverable: PR-driven status changes

---

# Explicitly Deferred

- time tracking
- gantt charts
- dependencies graph
- permissions
- server mode
- notifications
