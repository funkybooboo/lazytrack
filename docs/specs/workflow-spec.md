# Workflow Rules Specification — lazytrack

Workflow rules automate task state changes based on git and PR events.

**Location**: `.lazytrack/workflow.yaml`

Workflow automation is optional but recommended for teams using PR-based workflows.

---

## Overview

Workflow rules define **event → action** mappings that automatically update tasks when git or PR events occur.

**Benefits**:
- Reduce manual status updates
- Keep tasks in sync with code activity
- Enforce consistent workflow
- Auto-link commits and PRs

---

## Basic Example

```yaml
# .lazytrack/workflow.yaml

rules:
  # When a branch is created with task ID, mark as in progress
  - when: branch_created
    set_status: in_progress

  # When PR is opened, move to review
  - when: pr_opened
    set_status: review

  # When PR is merged, mark as done
  - when: pr_merged
    set_status: done

  # Auto-link commits that mention task ID
  - when: commit_detected
    add_link: commit
```

---

## Rule Structure

Each rule is an object with:

```yaml
- when: <event_type>       # Required: trigger event
  if: <conditions>         # Optional: filter conditions
  set_status: <status>     # Optional: status to set
  add_link: <link_type>    # Optional: link type to add
  set_field: <field_map>   # Optional: fields to update
  run_command: <command>   # Optional: command to execute (future)
```

---

## Supported Events

### Git Events

#### `branch_created`

Triggered when a new git branch is created and contains a task ID.

**Detection**:
- Branch name matches pattern from `config.yaml`: `git.branch_pattern`
- Default pattern: `(?i){prefix}[-_]\d+` (case-insensitive)

**Examples**:
- `LT-042-add-oauth` → triggers for `LT-042`
- `feature/lt-042-login` → triggers for `LT-042`
- `LT_042_refactor` → triggers for `LT-042`

**When Triggered**:
- On `git checkout -b <branch>` (via `post-checkout` hook)
- On `lazytrack sync` (detects current branch)

---

#### `branch_deleted`

Triggered when a branch containing a task ID is deleted.

**Detection**: Branch name contains task ID and is removed from git.

**When Triggered**:
- On `git branch -d <branch>` (via hook)
- On `lazytrack sync` (branch no longer exists)

---

#### `commit_detected`

Triggered when a commit message contains a task ID.

**Detection**:
- Commit message matches `git.commit_pattern` from config
- Default pattern: `{prefix}-\d+`

**Examples**:
- `LT-042 fix auth bug` → triggers for `LT-042`
- `Add feature (LT-042)` → triggers for `LT-042`
- `Multiple tasks LT-042 LT-043` → triggers for both

**When Triggered**:
- On `git commit` (via `post-commit` hook)
- On `lazytrack sync` (scans recent commits)

---

### Pull Request Events

#### `pr_opened`

Triggered when a PR is opened that references a task ID.

**Detection**:
- PR title or body contains task ID
- PR branch name contains task ID

**When Triggered**:
- On `lazytrack sync` with `gh`/`glab` CLI
- On manual PR link via `lazytrack link pr <id> <pr-number>`

---

#### `pr_closed`

Triggered when a PR is closed (but not merged).

**When Triggered**:
- On `lazytrack sync` (PR state changed to closed)

---

#### `pr_merged`

Triggered when a PR is successfully merged.

**When Triggered**:
- On `lazytrack sync` (PR state changed to merged)

---

#### `pr_draft`

Triggered when a PR is marked as draft.

**When Triggered**:
- On `lazytrack sync` (PR marked as draft)

---

#### `pr_ready`

Triggered when a PR is marked as ready for review (un-drafted).

**When Triggered**:
- On `lazytrack sync` (draft flag removed)

---

### Issue Events (Future)

These events are planned for future releases:

- `issue_opened`
- `issue_closed`
- `issue_labeled`

---

## Actions

### `set_status: <status>`

Updates the task's `status` field.

**Example**:
```yaml
- when: pr_merged
  set_status: done
```

**Validation**:
- Status must exist in `config.yaml` statuses list
- Error if status is invalid

---

### `add_link: <link_type>`

Adds a link to the task's metadata or Links section.

**Supported Link Types**:
- `commit` — Add commit SHA to `commits` array
- `branch` — Set `branch` field to branch name
- `pr` — Set `pr` field to PR number
- `issue` — Set `issue` field to issue number

**Example**:
```yaml
- when: commit_detected
  add_link: commit

- when: pr_opened
  add_link: pr
```

**Behavior**:
- `commit`: Appends to `commits` array (no duplicates)
- `branch`: Overwrites `branch` field
- `pr`: Overwrites `pr` field
- `issue`: Overwrites `issue` field

---

### `set_field: <field_map>`

Updates arbitrary task fields.

**Example**:
```yaml
- when: pr_merged
  set_field:
    status: done
    updated: "{{now}}"
    merged_at: "{{event.timestamp}}"
```

**Variables**:
- `{{now}}` — Current timestamp (ISO 8601)
- `{{event.timestamp}}` — Event timestamp
- `{{event.author}}` — Event author (git user or PR author)
- `{{event.branch}}` — Branch name
- `{{event.commit}}` — Commit SHA

---

### `run_command: <command>` (Future)

Execute a shell command when rule triggers.

**Example**:
```yaml
- when: pr_merged
  run_command: "notify-send 'Task {{task.id}} completed'"
```

**Security**: Must be explicitly enabled in config. Disabled by default.

---

## Conditions

### Basic Conditions

Filter rules with `if` clause:

```yaml
- when: pr_opened
  if:
    label: feature
  set_status: review
```

**Supported Conditions**:

| Field | Type | Description |
|-------|------|-------------|
| `status` | String | Current task status |
| `priority` | String | Task priority |
| `owner` | String | Task owner |
| `label` | String | Task has this label (any match) |
| `milestone` | String | Task milestone |
| `branch` | Regex | Branch name matches pattern |

---

### Multiple Conditions (AND)

```yaml
- when: pr_opened
  if:
    priority: high
    owner: nate
  set_status: urgent_review
```

All conditions must match.

---

### OR Conditions

```yaml
- when: commit_detected
  if:
    any:
      - label: bug
      - label: hotfix
  set_status: in_progress
```

---

### NOT Conditions

```yaml
- when: pr_merged
  if:
    not:
      label: no-auto-close
  set_status: done
```

---

### Regex Patterns

```yaml
- when: branch_created
  if:
    branch: "^feature/"
  set_status: in_progress
```

---

## Conflict Resolution

When multiple rules match the same event:

### Default Behavior: Last Rule Wins

```yaml
rules:
  - when: pr_merged
    set_status: review  # Applied first

  - when: pr_merged
    set_status: done    # This overwrites the previous
```

Result: Task status set to `done`.

---

### Explicit Priority

```yaml
rules:
  - when: pr_merged
    priority: 1
    set_status: review

  - when: pr_merged
    priority: 10
    set_status: done
```

Higher priority number = applied last.

---

### Stop Processing

```yaml
rules:
  - when: pr_merged
    if:
      label: manual-close
    # No action, effectively skips automation
    stop: true

  - when: pr_merged
    set_status: done  # Not applied if previous rule matched
```

---

## Complete Examples

### Standard Workflow

```yaml
# .lazytrack/workflow.yaml

rules:
  # Starting work
  - when: branch_created
    set_status: in_progress
    add_link: branch

  # Code review
  - when: pr_opened
    set_status: review
    add_link: pr

  # PR updates
  - when: pr_draft
    set_status: in_progress

  - when: pr_ready
    set_status: review

  # Completion
  - when: pr_merged
    set_status: done
    set_field:
      updated: "{{now}}"

  - when: pr_closed
    if:
      not:
        label: wont-fix
    set_status: todo  # Reopen if PR closed without merge

  # Always link commits
  - when: commit_detected
    add_link: commit
```

---

### Team Workflow with Priorities

```yaml
rules:
  # High priority tasks skip backlog
  - when: branch_created
    if:
      priority: critical
    set_status: in_progress

  - when: branch_created
    if:
      priority: high
    set_status: in_progress

  # Normal tasks go to ready first
  - when: branch_created
    set_status: ready

  # PR automation
  - when: pr_opened
    if:
      owner: nate
    set_status: self_review

  - when: pr_opened
    set_status: review

  # Bugs auto-merge to main go straight to done
  - when: pr_merged
    if:
      label: hotfix
    set_status: done

  # Features need QA
  - when: pr_merged
    if:
      label: feature
    set_status: qa

  # Everything else goes to done
  - when: pr_merged
    set_status: done
```

---

### Minimal Workflow

```yaml
# Just auto-link commits and PRs
rules:
  - when: commit_detected
    add_link: commit

  - when: pr_opened
    add_link: pr
```

---

### Advanced: Custom Fields

```yaml
rules:
  - when: pr_opened
    set_field:
      status: review
      reviewed_by: "{{event.author}}"
      review_started: "{{now}}"

  - when: pr_merged
    set_field:
      status: done
      merged_by: "{{event.author}}"
      merged_at: "{{event.timestamp}}"
      completed: "{{now}}"
```

---

## Execution Flow

When an event occurs:

1. **Event Detection**
   - Git hook fires, or `lazytrack sync` runs
   - Event type determined (e.g., `pr_merged`)

2. **Task Identification**
   - Extract task ID from branch/commit/PR
   - Load task file from `.lazytrack/tasks/<ID>.md`

3. **Rule Matching**
   - Find all rules where `when` matches event type
   - Evaluate `if` conditions for each rule
   - Filter to matching rules only

4. **Rule Application**
   - Sort by priority (if specified)
   - Apply rules in order
   - Each rule can modify task state
   - Stop if `stop: true` encountered

5. **Task Update**
   - Serialize updated task to file
   - Atomic write (no data loss)
   - Log action (future: audit trail)

6. **Post-Processing**
   - Update TUI if running
   - Log to stdout (in CLI mode)

---

## Safety Rules

Workflow automation **must never**:

- Delete tasks
- Delete task files
- Overwrite user-written markdown body sections
- Remove unknown frontmatter fields
- Modify task files outside `.lazytrack/`
- Execute arbitrary code (without explicit enable)

---

## Validation

On loading `workflow.yaml`:

1. **Syntax Check**: Valid YAML
2. **Structure Check**: Rules have `when` field
3. **Event Check**: Event types are recognized
4. **Action Check**: Actions are valid (e.g., status exists)
5. **Condition Check**: Condition fields are valid
6. **Regex Check**: Patterns compile successfully

**Invalid workflow** → lazytrack exits with error.

---

## Debugging

### Dry Run Mode

```bash
lazytrack sync --dry-run
```

Shows what rules would apply without making changes.

**Output**:
```
[DRY RUN] Would apply rule:
  Event: pr_merged
  Task: LT-042
  Action: set_status -> done
  Reason: PR #156 merged
```

---

### Verbose Logging

```bash
lazytrack sync --verbose
```

Shows detailed rule evaluation:

```
Evaluating rules for event: pr_merged
  Task: LT-042
  Rule 1: pr_merged -> set_status: review
    Condition: priority = high ... PASS
    Action: set_status -> review ... APPLIED
  Rule 2: pr_merged -> set_status: done
    Condition: none
    Action: set_status -> done ... APPLIED (overwrites previous)
Final state: status = done
```

---

### Disable Automation

Temporarily disable all automation:

```bash
LAZYTRACK_NO_AUTOMATION=1 git commit -m "..."
```

Or permanently in config:

```yaml
# config.yaml
automation:
  enabled: false
```

---

## Hook Installation

Install git hooks to enable automation:

```bash
lazytrack hook install
```

Installs hooks:
- `.git/hooks/post-commit` — Link commits
- `.git/hooks/post-checkout` — Detect branch switches
- `.git/hooks/prepare-commit-msg` — Suggest task ID (future)

**Safety**: Existing hooks are preserved (wrapped, not replaced).

---

## Performance

Workflow execution is designed to be fast:

- Rules evaluated in-memory (no disk I/O during matching)
- Task files loaded lazily
- Only affected tasks updated
- Parallel execution for multi-task events (future)

**Target**: < 50ms overhead per git operation.

---

## Migration Path

### v0.x to v1.0

Workflow format may change. Migration provided via:

```bash
lazytrack doctor --migrate-workflow
```

### v1.0+

Workflow format is stable. New event types and actions may be added, but:
- Existing rules remain valid
- Unknown fields preserved (for forward compatibility)

---

## Limitations

### Current Limitations (v0.x)

- No dependency tracking (e.g., "if LT-001 is done, update LT-002")
- No scheduled rules (e.g., "if overdue, notify")
- No multi-task rules (e.g., "update all tasks with label X")

These may be added in post-v1.0 releases.

---

## Security Considerations

### Git Hooks

- Hooks run in user context (same permissions as git)
- No network access required
- Malicious hooks could modify tasks arbitrarily

**Mitigation**: Review `.git/hooks/` contents after install.

### Command Execution (Future)

If `run_command` is implemented:
- Disabled by default
- Requires explicit opt-in
- Commands run in restricted environment
- No shell expansion (avoid injection)

---

## See Also

- [Configuration Specification](./config-spec.md)
- [Task File Specification](./task-file-spec.md)
- [Directory Structure](./directory-structure-spec.md)
