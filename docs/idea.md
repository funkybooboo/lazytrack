Good — that constraint (plain-text, repo-local, user-owned data) is actually a *huge design anchor*. It pushes you toward a focused, high-leverage tool instead of a Jira clone. Think:

> **git-native project tracking with a TUI front-end and smart automation**

Not “project management platform.”
More like: **workflow layer on top of git + PR + issues**.

Let’s tighten scope and design this properly.

---

# 🎯 Product Definition (Tight Scope)

**lazytrack = git-native project tracker TUI**

Core principles:

* ✅ Data lives in repo
* ✅ Plain text, human editable
* ✅ No server required
* ✅ TUI is an accelerator, not a gatekeeper
* ✅ Git = source of truth
* ✅ Git hosting = optional automation layer
* ✅ Works offline
* ✅ PR + commit aware

You are not replacing Jira.
You are replacing: *“spreadsheet + issues + TODO.md + mental overhead.”*

---

# 📦 Storage Model (Plain Text, Git-Friendly)

Use a predictable directory:

```
.lazytrack/
  config.yaml
  tasks/
    LT-001.md
    LT-002.md
  projects.md
  sprints.md
  people.md
  workflow.yaml
```

Each task = one markdown file.

---

## Task File Example

```
# LT-014 — Add OAuth login

status: in_progress
priority: high
owner: nate
labels: [auth, backend]
milestone: v0.3
created: 2026-02-03
due: 2026-02-10

## Description
Implement OAuth login via GitHub.

## Acceptance Criteria
- login button works
- callback handled
- session persisted

## Links
- pr: #142
- issue: #77
```

Design goals:

* readable raw
* diffable
* merge-safe
* grep-friendly
* scriptable
* no JSON — markdown + yaml frontmatter is ideal

---

# 🧠 Automation Layer (Your “Awesomeness” Factor)

This is where lazytrack becomes special.

## Git Hooks Integration

Optional hooks installed by lazytrack:

### commit message parsing

```
git commit -m "LT-014 fix callback bug"
→ auto-link commit to task
```

### PR title parsing

```
PR: LT-014 Add OAuth login
→ auto move status → review
```

---

## gh / gitlab / forge CLI Integration

lazytrack sync:

* pulls issue metadata
* maps issues ↔ tasks
* syncs PR status
* attaches links automatically

---

## Status Automation Rules

Example workflow.yaml:

```
rules:
  - when: pr_opened
    set_status: review

  - when: pr_merged
    set_status: done

  - when: branch_created
    set_status: in_progress
```

---

# 🖥️ TUI Feature Scope (Keep This Tight)

Do not build a monster. Focus on 4 views.

---

## 1️⃣ Task Board View

Kanban-style:

```
todo | in_progress | review | done
```

* move with keys
* filter
* search
* bulk edit
* label filter

---

## 2️⃣ Task Detail View

* edit markdown sections
* edit frontmatter fields via form UI
* show linked:

  * commits
  * PRs
  * branches

---

## 3️⃣ Git Awareness Panel

Per task:

```
Commits: 4
Open PR: #142
Branch: feature/oauth
Last activity: 2h ago
```

This is your killer feature.

---

## 4️⃣ Sprint / Milestone View

Simple — not Jira-complex:

* milestone = list of tasks
* show progress bar
* show blockers

No burndown charts v1.

---

# 🔧 Core Commands

Keep CLI small.

```
lazytrack init
lazytrack new
lazytrack tui
lazytrack sync
lazytrack hook install
lazytrack doctor
```

---

# 📌 What NOT To Build (Scope Guardrails)

Do NOT include:

* time tracking
* resource planning
* gantt charts
* budgeting
* dependency graphs v1
* org hierarchy
* permissions
* comments threads
* notifications server

Those explode scope.

---

# 💎 High-Value Features Per Line of Code

These give “wow” without platform bloat:

* PR → status automation
* commit → task linking
* branch → task linking
* keyboard-first TUI
* plain text ownership
* offline mode
* repo-local config
* smart grep/search
* markdown editing with preview
* task templates

---

# 🧭 Positioning Statement

If you want a crisp one-liner for the project:

> **lazytrack — a git-native, plain-text project tracker with a powerful terminal UI**

or

> **lazytrack — project tracking that lives in your repo**

