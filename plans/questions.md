# Planning Questions & Feature Ideas

This document contains open questions, feature ideas, and design decisions that need to be made before or during implementation.

---

## 🎯 Core Philosophy Questions

### 1. The "Basecamp + Jira" Vision

You mentioned wanting the power of both. Here's what each excels at:

**Basecamp strengths:**
- Hill Charts (visual progress tracking)
- Message boards (async discussions)
- Automatic check-ins ("What did you work on today?")
- To-do lists with simple hierarchy
- Campfire (real-time chat)
- Docs & Files in context

**Jira strengths:**
- Complex workflow states
- Sprint planning & velocity tracking
- Issue linking (blocks, relates to, duplicates)
- Advanced JQL queries
- Detailed time tracking
- Burndown/burnup charts

**Questions:**
1. Which specific features from each tool do you find most valuable in practice?
2. What features from these tools do you actively avoid or find annoying?
3. What's missing from both that you wish existed?

---

## 💡 Feature Ideas to Consider

### High-Value, Low-Complexity Features

#### 1. Task Dependencies (Post-v1.0)

```yaml
# In task frontmatter
blocks: [LT-012, LT-015]
blocked_by: [LT-008]
```

**TUI Display:**
```
LT-042 — Add OAuth login
  ⚠ Blocked by: LT-008 (in progress)
  🔒 Blocks: LT-012, LT-015
```

**Question:** Should we show dependency graphs in TUI (ASCII art) or just as lists?

---

#### 2. Subtasks / Checklists

Two approaches:

**A) Lightweight (in markdown):**
```markdown
## Subtasks
- [ ] Design OAuth flow
- [x] Setup GitHub app
- [ ] Implement callback handler
```

**B) Rich (separate task files):**
```yaml
parent: LT-042
subtasks: [LT-042-1, LT-042-2, LT-042-3]
```

**Question:** Which approach fits your workflow better? Or should we support both?

---

#### 3. Quick Notes / Comments

Store discussion without leaving TUI:

```markdown
## Discussion

**2026-02-04 @nate:**
Reviewed the OAuth library options. Going with `oauth2` crate.

**2026-02-05 @alice:**
Make sure to handle token refresh edge cases.
```

**Alternative:** Link to GitHub PR discussions instead?

**Question:** Do you want inline discussions, or prefer keeping conversations in PRs/issues?

---

#### 4. Task Estimates & Time Tracking

**Minimal approach:**
```yaml
estimate: 5  # hours or story points
actual: 8
```

**TUI display:**
```
Estimated: 5h | Actual: 8h (+3h over)
```

**Question:** You mentioned "not time tracking" in deferred features. Should we avoid this entirely, or is lightweight estimation okay?

---

#### 5. Board Views Beyond Kanban

**Ideas:**
- **List view** (flat, sortable table)
- **Milestone view** (grouped by milestone)
- **Owner view** (grouped by assignee)
- **Priority matrix** (2x2: urgent/important)
- **Timeline view** (text-based gantt)

**Question:** Which views would you actually use daily?

---

### Git-Native Power Features

#### 6. Stacked Diffs / PR Chains

Track multi-PR workflows:

```yaml
pr_chain:
  - pr: 142  # LT-042-part-1
  - pr: 145  # LT-042-part-2 (depends on 142)
  - pr: 148  # LT-042-part-3 (depends on 145)
```

**Useful for:** Breaking large features into reviewable chunks.

**Question:** Do you work with stacked diffs? Is this worth the complexity?

---

#### 7. Branch Patterns & Auto-linking

Enhanced branch detection:

```yaml
# config.yaml
git:
  branch_patterns:
    feature: "^feature/{prefix}[-_]{number}[-_].*"
    hotfix: "^hotfix/{prefix}[-_]{number}[-_].*"
    
  auto_labels:
    - pattern: "^feature/"
      labels: [feature]
    - pattern: "^hotfix/"
      labels: [hotfix, urgent]
```

**Question:** Useful or over-engineering?

---

#### 8. Commit Activity Heatmap

Show which tasks are actively being worked on:

```
Tasks by Activity (last 7 days):

LT-042 ████████████ 12 commits
LT-041 ████░░░░░░░░  4 commits
LT-039 ██░░░░░░░░░░  2 commits
```

**Question:** Would this help you identify stale work?

---

### Workflow Automation Ideas

#### 9. Smart Status Inference

Beyond simple rules, infer status from multiple signals:

```yaml
# Auto-detect "in review" when:
# - PR is open
# - No recent commits (>24h)
# - CI passed

auto_inference:
  - status: in_progress
    when:
      - commits_today: "> 0"
      OR
      - branch_active: true
      
  - status: stale
    when:
      - status: in_progress
      - no_activity_days: "> 14"
```

**Question:** Too magic? Or helpful?

---

#### 10. Task Lifecycle Hooks

Run scripts at task transitions:

```yaml
# workflow.yaml
hooks:
  on_status_change:
    to: done
    run: |
      # Auto-update changelog
      echo "- Completed: {{task.title}}" >> CHANGELOG.md
```

**Question:** Security risk vs. power user feature?

---

### Collaboration Features (Without Server)

#### 11. Review Requests

```yaml
reviewers: [alice, bob]
review_status:
  alice: approved
  bob: pending
```

**TUI shows:**
```
Reviews: ✓ alice  ⏳ bob
```

**Synced via:** Git commits to task file, or via `gh pr` status.

**Question:** Redundant with PR reviews, or useful for non-PR work?

---

#### 12. Daily Digest

Generate markdown summaries:

```bash
lazytrack digest --today
```

**Output:**
```markdown
# Daily Summary — 2026-02-04

## Completed (3)
- LT-041 — Fix auth redirect
- LT-038 — Update dependencies
- LT-035 — Add logging

## In Progress (5)
- LT-042 — Add OAuth login (8 commits today)
- ...

## Blocked (2)
- LT-039 — Waiting on design review
- ...
```

**Use case:** Standup reports, weekly updates.

**Question:** Useful or unnecessary ceremony?

---

## 🔍 Features to REMOVE / Simplify

Based on streamlining goals:

### Potential Cuts:

1. **Custom Statuses** — Stick to 5 standard (todo/in_progress/review/blocked/done)?
2. **Custom Priorities** — Stick to 4 standard (low/medium/high/critical)?
3. **Multiple Milestones per Task** — Always single milestone only?
4. **Task Archiving** — Just delete tasks or move to `archive/` directory?
5. **Due Dates** — Skip entirely? (You mentioned no gantt charts)
6. **TUI Mouse Support** — Drop entirely, stay keyboard-only?

**Question:** Any of these worth cutting for simplicity?

---

## 🎯 Streamlining Ideas

### Reduce Config Complexity

**Current:** Lots of YAML configuration.

**Alternative:** Convention over configuration.

```yaml
# Ultra-minimal config
project:
  prefix: "LT"

# Everything else uses defaults
```

**Question:** Should we lean harder into defaults?

---

### Reduce CLI Surface Area

**Current plan:** ~10 commands

**Alternative:** Just 3-4 core commands

```bash
lazytrack init
lazytrack tui      # Main interface
lazytrack sync     # Git integration
lazytrack [task]   # Shorthand for 'show'
```

Everything else done in TUI.

**Question:** CLI-first or TUI-first tool?

---

### Unified View Model

Instead of separate board/list/milestone views:

**Single "Focus Mode" View** with different filters:

```
[Filter: In Progress | Owner: Me | High Priority]

┌─ Focused Tasks ─────────────────────┐
│ LT-042 — Add OAuth login            │
│ LT-039 — Fix redirect bug           │
└─────────────────────────────────────┘
```

**Question:** One powerful view or multiple specialized views?

---

## 🚀 Implementation Strategy Questions

### Parser Approach

**Option A:** Hand-written parser (full control, complex)
**Option B:** Use `pulldown-cmark` + `serde_yaml` (simpler, less flexible)
**Option C:** Use `nom` for custom parsing (powerful, learning curve)

**Question:** What's more important: parsing speed or implementation speed?

---

### TUI Framework

**Confirmed:** ratatui

**Architecture question:**
- **Elm-style** (immutable state, update functions)
- **Component-based** (React-like)
- **Simple imperative** (direct state mutation)

**Question:** Any preference on TUI architecture patterns?

---

### Storage Format Flexibility

**Current spec:** Undelimited YAML frontmatter (unique format)

**Alternative:** Standard frontmatter with `---` delimiters

```markdown
---
status: in_progress
priority: high
---

# LT-042 — Add OAuth login

## Description
...
```

**Pros:** Compatible with more markdown tools
**Cons:** More visual noise

**Question:** Stick with undelimited or go standard?

---

### ID Format

**Current:** `LT-001` (zero-padded)

**Alternative options:**
- `LT-1` (no padding, simpler)
- `lt-001` (lowercase, filesystem friendly)
- `LT_001` (underscore, shell-friendly)

**Question:** Does padding matter for sorting/display?

---

## 🎨 UX Philosophy Questions

### Error Handling

**Approach A:** Strict validation, fail fast
```
Error: Invalid status 'in-progress' in LT-042.md
Valid statuses: todo, in_progress, review, blocked, done
```

**Approach B:** Lenient, warn and continue
```
Warning: Unknown status 'in-progress' in LT-042.md, defaulting to 'todo'
```

**Question:** Fail fast or be forgiving?

---

### Git Integration Level

**Minimal:** Just link commits/PRs
**Moderate:** Also detect branches, show activity
**Deep:** Parse commit messages for structured data, analyze code churn

**Question:** How deep should git integration go?

---

### Sync Strategy

**Option A:** Manual sync only (`lazytrack sync`)
**Option B:** Auto-sync on TUI launch
**Option C:** Background sync while TUI running

**Question:** Manual control or automatic convenience?

---

## 📊 Data Model Questions

### Task Relationships

Should we support:
- **Subtasks** (parent-child)?
- **Dependencies** (blocks/blocked-by)?
- **Related** (loose association)?
- **Duplicates** (same issue)?
- **Epics** (multiple tasks grouped)?

**Question:** Which relationships are essential vs. nice-to-have?

---

### Search/Filter Complexity

**Simple:**
```bash
lazytrack list --status in_progress --owner nate
```

**Advanced:**
```bash
lazytrack query "status:in_progress AND (priority:high OR label:bug)"
```

**Question:** Need advanced query language or keep it simple?

---

## 🤝 Recommendations (Initial Thoughts)

Based on "streamlined and to the point" philosophy:

### Include in v1.0 or Post-v1.0:
1. ✅ Task dependencies (blocks/blocked-by) — Core workflow feature
2. ✅ Subtasks as markdown checklists — Simple, no extra complexity
3. ✅ Daily digest command — Low complexity, high value
4. ✅ List view in TUI — Alternative to board
5. ✅ Standard frontmatter delimiters — Better compatibility

### Defer to v2.0+:
1. ⏳ Time estimates/tracking
2. ⏳ Stacked PR chains
3. ⏳ Advanced query language
4. ⏳ Task lifecycle hooks
5. ⏳ Review request tracking

### Consider Cutting Entirely:
1. ❌ Multiple milestones per task
2. ❌ Mouse support in TUI
3. ❌ Due dates (conflicts with "no gantt" philosophy)
4. ❌ Smart status inference (too magical)
5. ❌ Embedded discussions (use PR comments instead)

---

## 🗣️ Questions to Answer

**About Your Workflow:**

1. **What's your #1 pain point** with current tools (GitHub issues, Jira, etc.)?

2. **What would make you use lazytrack every day** vs. falling back to other tools?

3. **Team size:** Solo dev, small team (2-5), or larger? This impacts which features matter.

4. **Workflow style:** 
   - Fast iteration (many small PRs)?
   - Long-lived branches?
   - Trunk-based development?

5. **What features from the ideas list** excite you most?

6. **What feels like bloat** that we should avoid?

---

## 📝 Decision Log

Use this section to record decisions as you make them:

### Decision: [Topic]
**Date:** YYYY-MM-DD  
**Question:** What was being decided?  
**Decision:** What was chosen?  
**Rationale:** Why?  

---

### Decision: Example
**Date:** 2026-02-04  
**Question:** Should we use standard frontmatter delimiters?  
**Decision:** TBD  
**Rationale:** TBD  

---

## Next Steps

1. Review this document
2. Answer the key questions above
3. Update the roadmap with any new features/changes
4. Update specs to reflect decisions
5. Begin v0.1.0 implementation
