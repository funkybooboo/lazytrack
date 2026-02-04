# lazytrack Roadmap — Semantic Versioning

**Goal**: Deliver a production-ready git-native project tracker with clear version milestones.

**Tech Stack**: Rust (ratatui for TUI, clap for CLI)

---

## Version Strategy

- **v0.x.x** — Pre-release versions building toward MVP
- **v1.0.0** — Production-ready MVP with all core features
- **v1.x.x** — Post-MVP enhancements and additional features
- **v2.0.0+** — Major architectural changes (breaking changes only)

### Versioning Rules

**Pre-v1.0.0 (v0.x.x)**:
- Breaking changes allowed between minor versions
- Storage/config formats may evolve
- Migration tools provided when needed

**Post-v1.0.0 (v1.x.x)**:
- Strict semantic versioning
- No breaking changes in minor/patch releases
- Deprecation warnings before removal
- Backward compatible storage format

---

## 🏗️ v0.1.0 — Foundation

**Goal**: Parse and store tasks safely, basic CLI interaction

### Features
- Task file schema (markdown + YAML frontmatter)
- Parser & serializer (round-trip safe)
- Task ID generator (LT-XXX format)
- Repo detection (`.lazytrack/` directory)
- Config loader (`config.yaml`)
- Basic CLI commands:
  - `lazytrack init` — Initialize repo
  - `lazytrack new <title>` — Create task
  - `lazytrack list` — List all tasks
  - `lazytrack show <id>` — Show task details
  - `lazytrack doctor` — Validate setup

### Deliverable
Can create, read, and list tasks via CLI. Tasks stored as human-readable markdown files.

---

## 🎨 v0.2.0 — CLI Polish

**Goal**: Complete CLI with editing, filtering, scriptability

### Features
- `lazytrack edit <id>` — Edit task metadata
- Enhanced `list` with filters:
  - `--status`, `--owner`, `--label`, `--milestone`
- `--json` output mode for scripting
- Status transitions via CLI
- Validation and error handling
- Configuration management

### Deliverable
Fully scriptable CLI, usable without TUI for automation workflows.

---

## 🖥️ v0.3.0 — TUI Core

**Goal**: Daily-driver visual interface

### Features
- TUI framework setup (ratatui)
- Kanban board view (todo/in_progress/review/done columns)
- Task list navigation (vim-style j/k/h/l keys)
- Task detail panel
- Status movement between columns
- Basic keyboard shortcuts
- Help screen and quit flow

### Deliverable
Visual board interface, can manage tasks interactively in the terminal.

---

## ✏️ v0.4.0 — TUI Enhanced

**Goal**: Complete TUI editing experience

### Features
- In-TUI metadata editing
- Markdown section editing
- Search/filter panel (`/` key)
- Label/owner/milestone filters
- Quick task creation (`n` key)
- Visual status indicators
- Bulk selection basics

### Deliverable
Full-featured TUI, no need to drop to CLI for common task edits.

---

## 🔗 v0.5.0 — Git Awareness

**Goal**: Git-native task tracking

### Features
- Commit message parsing (detect `LT-XXX` patterns)
- Automatic commit linking
- Branch name detection
- Activity tracking per task
- Git context display in TUI
- Show linked commits in task detail view
- Recent activity indicators

### Deliverable
Tasks are git-aware, commits automatically linked to tasks.

---

## 🚀 v0.6.0 — PR Integration

**Goal**: PR-driven workflows

### Features
- `gh` CLI integration
- PR detection and linking
- PR status display in task view
- `lazytrack sync` command
- Manual PR linking support
- PR metadata in TUI

### Deliverable
Tasks can be linked to GitHub PRs, manual sync workflow available.

---

## ⚙️ v0.7.0 — Workflow Automation

**Goal**: Automated task state management

### Features
- Workflow rules engine (`workflow.yaml`)
- Event handlers:
  - `pr_opened` → status = review
  - `pr_merged` → status = done
  - `branch_created` → status = in_progress
  - `commit_contains_task_id` → auto-link
- `lazytrack hook install` command
- Git hook integration
- Configurable rules
- Rule conflict resolution

### Deliverable
Tasks update automatically based on git/PR events via workflow rules.

---

## 🎯 v0.8.0 — Polish & Stabilization

**Goal**: Production-ready quality (Release Candidate)

### Features
- Comprehensive error handling
- Edge case fixes
- Performance optimization
- Memory safety review
- Complete documentation
- User guide
- Migration guide for v1.0.0
- Testing coverage
- CI/CD pipeline
- Security audit

### Deliverable
Feature-complete, stable, well-documented. Ready for v1.0.0 release.

---

## ✅ v1.0.0 — MVP Release

**Goal**: Official stable production release

### Features
- All v0.x features stabilized
- Final bug fixes from RC testing
- Security hardening
- Release announcement
- Semver guarantees begin
- Long-term support commitment

### Deliverable
Production-ready git-native project tracker.

### Core Capabilities
1. ✅ Task management (CRUD, markdown storage)
2. ✅ CLI interface (full feature set, scriptable)
3. ✅ TUI board (kanban view, interactive editing)
4. ✅ Git integration (commit linking, branch detection)
5. ✅ PR integration (GitHub via `gh` CLI)
6. ✅ Workflow automation (rule-based status updates)

---

## 🚀 Post-v1.0.0 Roadmap

### v1.1.0 — Enhanced Search & Filtering
- Fuzzy search across all task content
- Saved filter presets
- Complex query language (AND/OR/NOT)
- Search history
- Filter templates

### v1.2.0 — Task Templates
- Template definitions in `.lazytrack/templates/`
- Template-based task creation
- Common workflow templates (bug, feature, spike)
- Custom template support
- Variable substitution

### v1.3.0 — Milestone & Sprint Views
- Dedicated milestone view in TUI
- Sprint planning interface
- Progress tracking and visualization
- Basic burndown chart (text-based)
- Due date management
- Milestone completion indicators

### v1.4.0 — Bulk Operations
- Multi-select in TUI (visual mode)
- Bulk status updates
- Bulk label management
- Batch editing
- Bulk archive/delete with confirmation

### v1.5.0 — GitLab Support
- `glab` CLI integration
- GitLab-specific automation rules
- Multi-platform provider support
- Provider abstraction layer
- Unified sync command

### v1.6.0 — Task Dependencies
- Blocker relationships (blocks/blocked-by)
- Dependency graph visualization (text-based)
- Blocked-by indicators in TUI
- Dependency validation
- Circular dependency detection

### v1.7.0 — Export & Reporting
- JSON export
- CSV export
- HTML report generation
- Markdown summaries
- Custom report templates
- Statistics and metrics

---

## 🔮 Future Considerations (v2.0.0+)

These would constitute **major versions** due to breaking changes or significant architectural shifts:

- Multi-repo workspace support
- Team collaboration features (comments, mentions)
- Custom field definitions (extend task schema)
- Plugin system architecture
- Alternative storage backends
- Optional API server mode
- Separate web UI project (read-only viewer)
- Real-time sync between team members

---

## Explicitly Deferred (Not in Scope)

To keep lazytrack focused and maintainable, these features are **explicitly not planned**:

- Time tracking
- Gantt charts / complex scheduling
- Resource management
- Budget tracking
- Permissions system
- Notification server
- Chat/messaging platform
- Hosted SaaS offering
- Mobile apps

---

## Development Priorities

### Critical Path to v1.0.0
1. **Storage layer** (v0.1.0) — Must be rock-solid, everything else depends on it
2. **CLI interface** (v0.1.0-v0.2.0) — Foundation for scripting and automation
3. **TUI experience** (v0.3.0-v0.4.0) — Primary user interface
4. **Git integration** (v0.5.0-v0.6.0) — Core differentiation from other tools
5. **Automation** (v0.7.0) — Killer feature that makes it "awesome"
6. **Stabilization** (v0.8.0) — Polish and production-ready quality

### Success Metrics for v1.0.0
- Can dogfood lazytrack to build lazytrack
- Zero data loss on round-trip file edits
- Fast enough for repos with 1000+ tasks
- Comprehensive documentation
- Active testing by early adopters
- Clean, maintainable codebase

---

## Release Cadence (Target)

- **v0.1.0 - v0.4.0**: ~2-3 weeks per release (foundation + TUI)
- **v0.5.0 - v0.7.0**: ~3-4 weeks per release (git integration + automation)
- **v0.8.0**: ~4-6 weeks (stabilization + testing)
- **v1.0.0**: After sufficient RC testing period

**Post-v1.0.0**: Minor releases every 4-8 weeks, patch releases as needed.

---

## Contributing to Roadmap

This roadmap is a living document. Priorities may shift based on:
- User feedback during v0.x releases
- Technical discoveries during implementation
- Community contributions
- Real-world usage patterns

See [CONTRIBUTING.md](../CONTRIBUTING.md) for how to propose roadmap changes.
