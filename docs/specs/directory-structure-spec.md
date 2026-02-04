# Directory Structure Specification — lazytrack

This document defines the complete directory structure and file organization for lazytrack repositories.

---

## Overview

All lazytrack data lives in the `.lazytrack/` directory at the repository root.

**Design Principles**:
- Plain text files
- Human-readable
- Git-friendly
- No hidden or binary formats
- Predictable locations

---

## Complete Structure

```
.lazytrack/
├── config.yaml              # Main configuration
├── workflow.yaml            # Workflow automation rules (optional)
├── tasks/                   # Task files directory
│   ├── LT-001.md
│   ├── LT-002.md
│   ├── LT-003.md
│   └── ...
├── templates/               # Task templates (optional)
│   ├── bug.md
│   ├── feature.md
│   └── spike.md
├── .gitignore              # Ignore temp files (optional)
└── .index.json             # Task metadata cache (future, gitignored)
```

---

## Root Directory: `.lazytrack/`

**Location**: Repository root (same level as `.git/`)

**Permissions**: Read/write for all repo collaborators

**Git Tracking**: Entire directory should be committed (except `.index.json`)

### Initialization

Created by `lazytrack init`:

```bash
$ lazytrack init
Initialized lazytrack in /path/to/repo
Created .lazytrack/config.yaml
Created .lazytrack/tasks/
```

---

## `config.yaml`

**Path**: `.lazytrack/config.yaml`

**Purpose**: Main configuration file

**Format**: YAML

**Required**: Yes

**Git Tracking**: Yes (committed)

**See**: [Configuration Specification](./config-spec.md)

**Example**:
```yaml
project:
  name: "My Project"
  prefix: "MP"

task_counter: 42

statuses:
  - id: "todo"
    label: "To Do"
  # ...
```

---

## `workflow.yaml`

**Path**: `.lazytrack/workflow.yaml`

**Purpose**: Workflow automation rules

**Format**: YAML

**Required**: No (automation is optional)

**Git Tracking**: Yes (committed)

**See**: [Workflow Specification](./workflow-spec.md)

**Example**:
```yaml
rules:
  - when: pr_merged
    set_status: done
```

**If Missing**: No automation applied (manual workflow only)

---

## `tasks/` Directory

**Path**: `.lazytrack/tasks/`

**Purpose**: Stores all task files

**Required**: Yes (created on init)

**Git Tracking**: Yes (committed)

### File Naming

**Format**: `<PREFIX>-<NUMBER>.md`

**Examples**:
- `LT-001.md`
- `LT-042.md`
- `API-1234.md`

**Rules**:
- Must match pattern: `^[A-Z]+-\d+\.md$`
- Numbers zero-padded to 3 digits minimum
- Sequential (no gaps allowed)

### Organization

Tasks are **flat** (no subdirectories):

```
tasks/
├── LT-001.md
├── LT-002.md
├── LT-003.md
└── LT-999.md
```

**No subdirectories**: All tasks in single directory for simplicity.

**Filtering**: Use labels/milestones in frontmatter, not directories.

**Rationale**: 
- Simple file operations
- Fast glob patterns
- Easy to grep
- No path confusion

---

## `templates/` Directory (Optional)

**Path**: `.lazytrack/templates/`

**Purpose**: Task templates for common workflows

**Required**: No

**Git Tracking**: Yes (recommended)

### Template Files

**Format**: Markdown with variable placeholders

**Naming**: `<template-name>.md`

**Examples**:
```
templates/
├── bug.md
├── feature.md
├── spike.md
└── refactor.md
```

### Template Variables

Variables replaced on task creation:

| Variable | Replaced With | Example |
|----------|---------------|---------|
| `{{ID}}` | Task ID | `LT-042` |
| `{{TITLE}}` | Task title | `Fix auth bug` |
| `{{DATE}}` | Current date | `2026-02-04` |
| `{{OWNER}}` | Git user | `nate` |
| `{{MILESTONE}}` | Current milestone | `v1.0.0` |

**Example Template** (`templates/bug.md`):
```markdown
# {{ID}} — {{TITLE}}

status: todo
priority: high
owner: {{OWNER}}
labels: [bug]
created: {{DATE}}

## Description

Brief description of the bug.
```

**Usage**:
```bash
lazytrack new --template bug "Fix login redirect"
```

**See**: [docs/templates/](../templates/) for examples

---

## `.gitignore`

**Path**: `.lazytrack/.gitignore`

**Purpose**: Ignore temporary and cache files

**Required**: No (but recommended)

**Content**:
```gitignore
# Temporary files
.tmp-*.md
*.swp
*~

# Cache (future)
.index.json
.cache/

# Backups
*.backup
```

**Auto-created**: On `lazytrack init` if not present

---

## `.index.json` (Future)

**Path**: `.lazytrack/.index.json`

**Purpose**: Performance cache for task metadata

**Required**: No

**Git Tracking**: No (gitignored)

**Format**: JSON

**Regenerated**: Automatically on file changes

**Example Structure**:
```json
{
  "version": 1,
  "generated_at": "2026-02-04T12:00:00Z",
  "tasks": [
    {
      "id": "LT-001",
      "title": "Setup repository",
      "status": "done",
      "priority": "high",
      "labels": ["setup"],
      "file": "tasks/LT-001.md",
      "updated": "2026-02-03"
    }
  ]
}
```

**Benefits**:
- Fast task listing (no file I/O)
- Quick filtering/searching
- Reduced parse overhead

**Invalidation**: Regenerated if:
- Task file modified (mtime check)
- Index file missing
- Index version mismatch

**Fallback**: If corrupted, lazytrack reads task files directly.

---

## Git Integration

### Tracked Files

**Always committed**:
- `config.yaml`
- `workflow.yaml` (if present)
- All files in `tasks/`
- All files in `templates/` (recommended)

**Never committed**:
- `.index.json` (cache)
- `.tmp-*` files (temporary)
- Backup files (`*.backup`)

### `.gitignore` Example

Add to repository root `.gitignore`:

```gitignore
# lazytrack cache (if .lazytrack/.gitignore not present)
.lazytrack/.index.json
.lazytrack/.tmp-*
.lazytrack/*.backup
```

---

## Lifecycle

### Initialization

```bash
$ lazytrack init
```

Creates:
1. `.lazytrack/` directory
2. `config.yaml` with defaults
3. `tasks/` directory (empty)
4. `.gitignore` (optional)

**Idempotent**: Running `lazytrack init` again is safe (won't overwrite existing config).

---

### Normal Operation

**Creating Tasks**:
```bash
$ lazytrack new "Add dark mode"
Created LT-042 at .lazytrack/tasks/LT-042.md
```

**File created**: `.lazytrack/tasks/LT-042.md`  
**Config updated**: `task_counter: 43`

**Editing Tasks**:
- Via TUI: Changes written to file atomically
- Via CLI: `lazytrack edit LT-042`
- Manually: Edit `.lazytrack/tasks/LT-042.md` directly

**Deleting Tasks**:
```bash
$ lazytrack delete LT-042
```

**File deleted**: `.lazytrack/tasks/LT-042.md`  
**Note**: ID is not reused (counter doesn't decrement)

---

### Migration

On major version upgrades, lazytrack may need to migrate files:

```bash
$ lazytrack doctor --migrate
```

**Migration Process**:
1. Detect old version
2. Create backup: `.lazytrack.backup.<timestamp>/`
3. Migrate files in-place
4. Validate migrated structure
5. Update version marker

**Backup Location**: Same level as `.lazytrack/`

```
repo/
├── .lazytrack/              # Current
├── .lazytrack.backup.20260204120000/  # Backup
└── .git/
```

---

## Performance Considerations

### Large Repos (1000+ Tasks)

**Strategies**:
- Use `.index.json` cache (future)
- Lazy file loading (load on-demand)
- Parallel file reads
- mmap for large files (future)

**Target Performance**:
- `lazytrack list`: < 100ms for 1000 tasks
- `lazytrack show LT-042`: < 50ms
- TUI startup: < 200ms for 1000 tasks

### Disk Space

**Typical Task File**: 1-5 KB

**1000 tasks**: ~5 MB

**Git History**: Task updates create new commits, but file diffs are small.

**Recommendation**: Run `git gc` periodically if repo grows large.

---

## Multi-User Scenarios

### Concurrent Edits

**Task Files**: Git merge handles most conflicts cleanly.

**Config File**: Avoid concurrent edits to `config.yaml`. Last write wins.

**task_counter**: Race condition possible if two users run `lazytrack new` simultaneously.

**Mitigation** (future):
- File locking
- Optimistic concurrency (detect conflicts)
- Retry with new ID on conflict

**Current Behavior**: Rare ID collision possible (one task overwrites another). Mitigated by reviewing git diffs before push.

---

### Shared Workflows

**Workflow File**: Committed to repo, shared by all users.

**Team Best Practice**:
1. One person owns workflow configuration
2. Changes reviewed via PR
3. Test workflow rules before merging

---

## Security

### Permissions

`.lazytrack/` directory:
- Readable: All repo collaborators
- Writable: All repo collaborators

No separate permission system (relies on git/filesystem permissions).

### Sensitive Data

**Do not store**:
- API keys
- Passwords
- Tokens
- Personal info

**Use links instead**:
```markdown
## Links

- credentials: [internal wiki](https://wiki.example.com/creds)
```

### Git Hooks

Hooks installed to `.git/hooks/`:
- Writable by repo owner
- Not tracked by git (local only)
- Review before install: `lazytrack hook show`

---

## Portability

### Cross-Platform

- Use forward slashes: `.lazytrack/tasks/LT-001.md`
- UTF-8 encoding required
- Line endings: Normalized by git (CRLF → LF)
- Case-sensitive filenames (even on case-insensitive filesystems)

### Backup

**Full Backup**: Copy entire `.lazytrack/` directory

**Cloud Sync**: Works (but may conflict on concurrent edits)

**Export**: `lazytrack export --format json > backup.json` (future)

---

## Examples

### Solo Developer Setup

```
.lazytrack/
├── config.yaml
├── tasks/
│   ├── LT-001.md
│   ├── LT-002.md
│   └── LT-003.md
└── .gitignore
```

Minimal setup, no automation.

---

### Team Project Setup

```
.lazytrack/
├── config.yaml
├── workflow.yaml
├── tasks/
│   ├── API-001.md
│   ├── API-002.md
│   ├── ...
│   └── API-156.md
├── templates/
│   ├── bug.md
│   ├── feature.md
│   └── spike.md
└── .gitignore
```

Full setup with workflow automation and templates.

---

### Large Project (1000+ Tasks)

```
.lazytrack/
├── config.yaml
├── workflow.yaml
├── tasks/
│   ├── PROJ-0001.md
│   ├── PROJ-0002.md
│   ├── ...
│   └── PROJ-1234.md
├── templates/
│   ├── bug.md
│   ├── feature.md
│   ├── epic.md
│   └── hotfix.md
├── .index.json  (gitignored cache)
└── .gitignore
```

With index cache for performance.

---

## Troubleshooting

### `config.yaml` Missing

```bash
$ lazytrack tui
Error: .lazytrack/config.yaml not found
Run: lazytrack init
```

**Fix**: Run `lazytrack init`

---

### `tasks/` Directory Missing

```bash
$ lazytrack list
Error: .lazytrack/tasks/ directory not found
```

**Fix**: 
```bash
mkdir -p .lazytrack/tasks
```

Or re-run `lazytrack init`

---

### Corrupted Index

```bash
$ lazytrack list
Warning: .index.json corrupted, regenerating...
```

**Auto-fix**: lazytrack regenerates index automatically.

---

### Task ID Conflict

```bash
$ git pull
CONFLICT (content): Merge conflict in .lazytrack/tasks/LT-042.md
```

**Resolution**: Standard git merge resolution (manual or tool-assisted)

---

## See Also

- [Configuration Specification](./config-spec.md)
- [Task File Specification](./task-file-spec.md)
- [Workflow Rules Specification](./workflow-spec.md)
- [Template Examples](../templates/)
