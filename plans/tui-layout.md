# TUI Layout Blueprint — lazytrack

The TUI is keyboard-first and panel-based.

No mouse required.

---

# Primary Layout

Status Columns
todo | in_progress | review | done

Task List

Task Preview / Metadata Panel

---

# Views

## Board View (default)

- kanban columns
- move with keys
- quick status change

## Task Detail View

- edit metadata fields
- edit markdown sections
- show git links

## Filter/Search View

- fuzzy search
- label filter
- owner filter
- milestone filter

---

# Keybindings (Example)

j/k        move
h/l        column switch
enter      open task
e          edit
s          change status
/          search
f          filter
n          new task
q          quit

---

# Interaction Rules

- no destructive action without confirm
- visible status transitions
- undo where feasible
