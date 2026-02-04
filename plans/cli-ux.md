# CLI UX Design — lazytrack

CLI commands must be:

- short
- predictable
- scriptable
- composable

---

# Command Set (MVP)

lazytrack init
lazytrack new
lazytrack list
lazytrack show LT-014
lazytrack edit LT-014
lazytrack tui
lazytrack sync
lazytrack hook install
lazytrack doctor

---

# Output Rules

- default = human readable
- --json flag for scripting
- errors to stderr
- exit codes must be meaningful

---

# Examples

Create task:

lazytrack new "Add OAuth login"

List tasks:

lazytrack list --status in_progress

Script usage:

lazytrack list --json

---

# Flags

Common flags:

--repo
--config
--json
--no-color
--yes
