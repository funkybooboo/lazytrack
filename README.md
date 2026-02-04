# lazytrack

**lazytrack** is a git-native, plain-text project tracker with a powerful terminal UI.

It gives you the most useful parts of project planning and work tracking — tasks, status, milestones, and workflow — while keeping all data stored directly in your repository as human-readable text files. No servers, no lock-in, no proprietary database.

Think: Basecamp / Jira / Monday-style tracking — but repo-local, terminal-first, and automation-aware.

---

# Why lazytrack

Most project tools separate your work tracking from your code. lazytrack does the opposite.

Your project data:

- lives in your repo
- is stored as plain text
- is versioned with git
- works offline
- is editable without the tool
- can be scripted and grepped
- can be automated via git + PR workflows

The TUI is an accelerator — not a gatekeeper.

---

# Core Principles

- **Git is the source of truth**
- **Plain text data ownership**
- **Repo-local storage**
- **Terminal-first UX**
- **Keyboard-driven workflow**
- **Automation over process**
- **Small, focused scope**
- **No server required**

---

# Features (MVP Scope)

## Task Tracking

- Task files stored as markdown
- YAML frontmatter metadata
- Status, priority, labels, owner, milestone
- Templates for fast creation
- Bulk edits and filters

## Terminal UI

- Kanban-style board view
- Task detail editor
- Keyboard-first navigation
- Fast search and filtering
- Status drag/move between columns

## Git Awareness

lazytrack understands your repo activity:

- link commits to tasks
- link branches to tasks
- show related PRs
- show recent activity per task

Example commit message:

```
LT-014 fix oauth callback bug
```

Automatically links commit to task.

## Git Hosting Integration

Optional integration with hosting CLIs:

- gh (GitHub CLI)
- glab (GitLab CLI)
- others later

Capabilities:

- sync PR status
- link PRs to tasks
- auto-update task status from PR events

## Automation Rules

Configurable workflow rules:

```
pr_opened → status = review
pr_merged → status = done
branch_created → status = in_progress
```

---

# Storage Format

All data lives in your repo:

```
.lazytrack/
  config.yaml
  workflow.yaml
  tasks/
    LT-001.md
    LT-002.md
  milestones.md
```

Example task file:

```markdown
# LT-014 — Add OAuth login

status: in_progress
priority: high
owner: nate
labels: [auth, backend]
milestone: v0.3
created: 2026-02-03

## Description
Implement OAuth login via GitHub.

## Acceptance Criteria
- login button works
- callback handled
- session persisted

## Links
- pr: #142
```

Readable without lazytrack. Editable without lazytrack.

---

# Commands

```
lazytrack init        # initialize repo tracking
lazytrack new         # create task
lazytrack tui         # open terminal UI
lazytrack sync        # sync with hosting CLI
lazytrack hook install
lazytrack doctor
```

---

# What lazytrack Is Not

To keep the tool fast and maintainable, lazytrack intentionally does **not** try to be:

- a Jira clone
- a time tracker
- a gantt planner
- a resource manager
- a hosted SaaS
- a permissions system
- a chat platform

It focuses on **practical project tracking for developers working in git repos**.

---

# Ideal Use Cases

- small teams
- OSS projects
- solo developers
- infra repos
- toolchains
- CLI-first workflows
- offline-first work
- git-centric teams

---

# Roadmap (Early Direction)

- core TUI board + editor
- markdown task schema
- git commit linking
- PR linking via gh
- automation rules
- templates
- sprint/milestone view

---

# Philosophy

> Your project plan should version with your code.

