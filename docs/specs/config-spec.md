# Configuration Specification — lazytrack

The main configuration file controls lazytrack behavior and project settings.

**Location**: `.lazytrack/config.yaml`

---

## Example Configuration

```yaml
# Project metadata
project:
  name: "My Project"
  description: "Project tracking for my awesome app"
  prefix: "LT"  # Task ID prefix (e.g., LT-001)

# Task counter (auto-incremented)
task_counter: 42

# Default values for new tasks
defaults:
  status: "todo"
  priority: "medium"
  owner: null
  labels: []

# Status workflow configuration
statuses:
  - id: "todo"
    label: "To Do"
    color: "gray"
  - id: "in_progress"
    label: "In Progress"
    color: "blue"
  - id: "review"
    label: "Review"
    color: "yellow"
  - id: "blocked"
    label: "Blocked"
    color: "red"
  - id: "done"
    label: "Done"
    color: "green"

# Priority levels
priorities:
  - id: "low"
    label: "Low"
    weight: 1
  - id: "medium"
    label: "Medium"
    weight: 2
  - id: "high"
    label: "High"
    weight: 3
  - id: "critical"
    label: "Critical"
    weight: 4

# Git integration settings
git:
  enabled: true
  # Pattern to detect task IDs in commit messages
  # {prefix} is replaced with project.prefix value
  commit_pattern: "{prefix}-\\d+"
  # Pattern to detect task IDs in branch names
  branch_pattern: "(?i){prefix}[-_]\\d+"

# Git hosting integration
hosting:
  # Supported: github, gitlab, gitea, none
  provider: "github"
  # CLI tool to use (gh, glab, etc.)
  cli_tool: "gh"
  # Auto-sync on lazytrack commands
  auto_sync: false
  # Link format for PRs/issues
  pr_url_template: "https://github.com/{owner}/{repo}/pull/{number}"
  issue_url_template: "https://github.com/{owner}/{repo}/issues/{number}"

# TUI settings
tui:
  # Vim-style keybindings
  vim_keys: true
  # Show git info in task detail
  show_git_links: true
  # Board columns to display
  board_columns:
    - "todo"
    - "in_progress"
    - "review"
    - "done"
  # Mouse support
  mouse_enabled: false
  # Default sort order: created, updated, priority, status
  sort_by: "priority"
  sort_order: "desc"

# CLI settings
cli:
  # Default output format: human, json
  output_format: "human"
  # Use color in output
  color: true
  # Pager for long output (less, more, none)
  pager: "less"

# File watching (future feature)
watch:
  enabled: false
  # Reload on external file changes
  auto_reload: true

# Hooks configuration
hooks:
  # Auto-install git hooks on init
  auto_install: false
  # Hooks to install
  install:
    - "post-commit"
    - "post-checkout"
```

---

## Field Specifications

### `project`

**Type**: Object  
**Required**: Yes

Project metadata and identification.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | String | Yes | Project display name |
| `description` | String | No | Project description |
| `prefix` | String | Yes | Task ID prefix (2-5 uppercase letters) |

**Validation**:
- `prefix` must match `^[A-Z]{2,5}$`
- `prefix` should be unique within your organization

---

### `task_counter`

**Type**: Integer  
**Required**: Yes  
**Default**: 1

Auto-incrementing counter for task IDs. Managed by lazytrack, should not be manually edited.

---

### `defaults`

**Type**: Object  
**Required**: No

Default values applied when creating new tasks.

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `status` | String | `"todo"` | Default status for new tasks |
| `priority` | String | `"medium"` | Default priority |
| `owner` | String | null | Default owner (git user if null) |
| `labels` | Array[String] | `[]` | Default labels |

---

### `statuses`

**Type**: Array[Object]  
**Required**: Yes

Defines available task statuses and their display properties.

**Status Object**:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | String | Yes | Status identifier (used in task files) |
| `label` | String | Yes | Display name |
| `color` | String | No | Color hint for TUI (gray, red, green, blue, yellow, cyan, magenta) |

**Standard Statuses**: `todo`, `in_progress`, `review`, `blocked`, `done`

Custom statuses are allowed but must be referenced in workflow rules.

---

### `priorities`

**Type**: Array[Object]  
**Required**: No

Defines priority levels and their sorting weight.

**Priority Object**:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | String | Yes | Priority identifier |
| `label` | String | Yes | Display name |
| `weight` | Integer | Yes | Numeric weight for sorting (higher = more important) |

**Standard Priorities**: `low`, `medium`, `high`, `critical`

---

### `git`

**Type**: Object  
**Required**: No

Git integration settings.

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `enabled` | Boolean | `true` | Enable git integration |
| `commit_pattern` | String | `"{prefix}-\\d+"` | Regex to detect task IDs in commits |
| `branch_pattern` | String | `"(?i){prefix}[-_]\\d+"` | Regex for branch names (case-insensitive) |

**Pattern Variables**:
- `{prefix}` — Replaced with `project.prefix` value

---

### `hosting`

**Type**: Object  
**Required**: No

Git hosting platform integration (GitHub, GitLab, etc.).

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `provider` | String | `"github"` | Hosting provider: `github`, `gitlab`, `gitea`, `none` |
| `cli_tool` | String | `"gh"` | CLI tool to use: `gh`, `glab` |
| `auto_sync` | Boolean | `false` | Auto-sync on commands |
| `pr_url_template` | String | (varies) | URL template for PRs |
| `issue_url_template` | String | (varies) | URL template for issues |

**URL Template Variables**:
- `{owner}` — Repository owner
- `{repo}` — Repository name
- `{number}` — PR/issue number

---

### `tui`

**Type**: Object  
**Required**: No

Terminal UI configuration.

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `vim_keys` | Boolean | `true` | Use vim-style navigation |
| `show_git_links` | Boolean | `true` | Show git links in detail view |
| `board_columns` | Array[String] | All statuses | Which status columns to display |
| `mouse_enabled` | Boolean | `false` | Enable mouse support |
| `sort_by` | String | `"priority"` | Default sort: `created`, `updated`, `priority`, `status` |
| `sort_order` | String | `"desc"` | Sort order: `asc`, `desc` |

---

### `cli`

**Type**: Object  
**Required**: No

Command-line interface settings.

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `output_format` | String | `"human"` | Default output: `human`, `json` |
| `color` | Boolean | `true` | Use color in terminal output |
| `pager` | String | `"less"` | Pager for long output: `less`, `more`, `none` |

---

### `hooks`

**Type**: Object  
**Required**: No

Git hooks configuration.

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `auto_install` | Boolean | `false` | Auto-install hooks on `lazytrack init` |
| `install` | Array[String] | `["post-commit"]` | Which hooks to install |

**Supported Hooks**:
- `post-commit` — Link commits to tasks
- `post-checkout` — Detect branch switches
- `prepare-commit-msg` — Suggest task ID in commit message

---

## Configuration Lifecycle

### Initialization

When running `lazytrack init`, a default config is created with:
- Project name from git repo name
- Prefix generated from project name (first 2-3 letters)
- Standard statuses and priorities
- Git integration enabled
- Detected hosting provider

### Updates

Config can be updated:
1. Manually editing `.lazytrack/config.yaml`
2. Via `lazytrack config set <key> <value>` (future feature)
3. Via TUI settings panel (future feature)

### Validation

On load, lazytrack validates:
- Required fields present
- Status IDs are unique
- Priority weights are positive integers
- Regex patterns are valid
- References to statuses/priorities exist

**Invalid configs** cause lazytrack to exit with error message and line number.

---

## Backward Compatibility

**v0.x versions**: Config format may change between releases.  
**v1.0.0+**: Config format is stable. New fields added with defaults.

### Migration

When config format changes, lazytrack will:
1. Detect old format version
2. Auto-migrate to new format
3. Create backup at `.lazytrack/config.yaml.backup`
4. Log migration changes

---

## Environment Variables

Config values can be overridden with environment variables:

```bash
LAZYTRACK_OUTPUT_FORMAT=json lazytrack list
LAZYTRACK_NO_COLOR=1 lazytrack show LT-001
```

**Format**: `LAZYTRACK_<SECTION>_<KEY>=<value>`

Examples:
- `LAZYTRACK_CLI_OUTPUT_FORMAT=json`
- `LAZYTRACK_TUI_VIM_KEYS=false`
- `LAZYTRACK_GIT_ENABLED=false`

---

## Examples

### Minimal Config

```yaml
project:
  name: "MyApp"
  prefix: "MA"

task_counter: 1
```

### Team Config

```yaml
project:
  name: "Backend API"
  prefix: "API"
  description: "REST API service"

task_counter: 156

defaults:
  status: "backlog"
  priority: "medium"
  labels: ["api"]

statuses:
  - id: "backlog"
    label: "Backlog"
    color: "gray"
  - id: "ready"
    label: "Ready"
    color: "cyan"
  - id: "in_progress"
    label: "In Progress"
    color: "blue"
  - id: "review"
    label: "Code Review"
    color: "yellow"
  - id: "qa"
    label: "QA Testing"
    color: "yellow"
  - id: "done"
    label: "Done"
    color: "green"

hosting:
  provider: "github"
  cli_tool: "gh"
  auto_sync: true
```

### Solo Developer Config

```yaml
project:
  name: "lazytrack"
  prefix: "LT"

task_counter: 42

defaults:
  owner: "nate"
  priority: "medium"

tui:
  vim_keys: true
  board_columns: ["todo", "in_progress", "done"]
  sort_by: "created"

git:
  enabled: true

hosting:
  provider: "github"
  auto_sync: false
```

---

## See Also

- [Task File Specification](./task-file-spec.md)
- [Workflow Rules Specification](./workflow-spec.md)
- [Directory Structure](./directory-structure-spec.md)
