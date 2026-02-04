# Task File Specification — lazytrack

Each task is stored as a single markdown file with YAML frontmatter.

**Location**: `.lazytrack/tasks/<ID>.md`

Task files must be:
- Human-readable
- Diff-friendly
- Merge-friendly
- Editable without lazytrack

---

## File Naming

**Format**: `<PREFIX>-<NUMBER>.md`

**Examples**:
- `LT-001.md`
- `LT-042.md`
- `API-1234.md`

**Rules**:
- Prefix configured in `config.yaml` (typically 2-5 uppercase letters)
- Number is zero-padded to 3 digits minimum
- Numbers assigned sequentially via `task_counter`
- File extension must be `.md`

---

## File Structure

A task file contains three parts:

1. **Title Line** — H1 heading with task ID and title
2. **Frontmatter** — YAML metadata block (undelimited)
3. **Body** — Markdown content sections

---

## Example Task File

```markdown
# LT-014 — Add OAuth login

status: in_progress
priority: high
owner: nate
labels: [auth, backend]
milestone: v0.3
created: 2026-02-03
updated: 2026-02-05

## Description

Implement OAuth login via GitHub. Users should be able to authenticate
using their GitHub account instead of username/password.

## Acceptance Criteria

- [ ] Login button on homepage redirects to GitHub OAuth
- [ ] OAuth callback handler processes the response
- [ ] User session is created and persisted
- [ ] Error handling for failed auth attempts

## Technical Notes

Need to handle token refresh edge cases. Consider using the `oauth2` crate
for token management.

Related to security audit from Q1.

## Links

- pr: #142
- issue: #77
- commit: a3f8d9c
- branch: feature/oauth-login
```

---

## Title Line

**Format**: `# <ID> — <Title>`

**Rules**:
- Must be first line of file
- Must start with `#` (H1 heading)
- Space after `#`
- Task ID in format `<PREFIX>-<NUMBER>`
- Space + em-dash (`—`) + space separator
- Title is free-form text (single line)

**Valid Examples**:
```markdown
# LT-001 — Setup project repository
# API-042 — Fix authentication bug
# DOCS-5 — Update API documentation
```

**Invalid Examples**:
```markdown
## LT-001 — Wrong heading level
# LT-001 - Wrong separator (hyphen instead of em-dash)
# LT-001 Missing title
LT-001 — Missing heading marker
```

---

## Frontmatter

**Format**: YAML key-value pairs (undelimited, no `---` markers)

**Location**: Immediately after title line, before first H2 heading

**Parsing Rules**:
1. Starts on line 2 (after blank line recommended)
2. Continues until first H2 heading (`##`) or end of file
3. Standard YAML syntax
4. Empty lines ignored
5. Comments (`#`) preserved

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `status` | String | Current task status (must match config.yaml statuses) |
| `created` | Date | Creation timestamp (ISO 8601 date: `YYYY-MM-DD`) |

### Standard Fields

| Field | Type | Example | Description |
|-------|------|---------|-------------|
| `priority` | String | `high` | Priority level (must match config.yaml) |
| `owner` | String | `nate` | Task owner/assignee |
| `labels` | Array[String] | `[bug, frontend]` | Categorization tags |
| `milestone` | String | `v1.0.0` | Associated milestone |
| `updated` | Date | `2026-02-05` | Last update timestamp |
| `due` | Date | `2026-03-01` | Due date (optional) |

### Auto-Managed Fields

These fields are managed by lazytrack automation:

| Field | Type | Example | Description |
|-------|------|---------|-------------|
| `commits` | Array[String] | `[a3f8d9c, b2e1f4d]` | Linked commit SHAs |
| `branch` | String | `feature/oauth` | Associated git branch |
| `pr` | Integer | `142` | Linked pull request number |
| `issue` | Integer | `77` | Linked issue number |

### Custom Fields

Additional fields are allowed and will be preserved:

```yaml
epic: "User Authentication"
estimate: 5
sprint: "Sprint 23"
blocked_by: "LT-012"
```

**Rules for Custom Fields**:
- Must be valid YAML
- Preserved during read/write
- Not validated by lazytrack
- Available in filters and queries

---

## Frontmatter Examples

### Minimal (Required Only)

```yaml
status: todo
created: 2026-02-03
```

### Standard Task

```yaml
status: in_progress
priority: medium
owner: alice
labels: [backend, api]
milestone: v1.2.0
created: 2026-02-01
updated: 2026-02-04
```

### Fully Populated

```yaml
status: review
priority: high
owner: nate
labels: [security, auth, backend]
milestone: v0.3
created: 2026-02-03
updated: 2026-02-05
due: 2026-02-10
commits: [a3f8d9c, b2e1f4d]
branch: feature/oauth-login
pr: 142
issue: 77
epic: "Authentication System"
estimate: 8
```

---

## Body Sections

**Format**: Markdown H2/H3 headings with content

**Recommended Sections**:

### `## Description`

Free-form description of the task. What needs to be done and why.

### `## Acceptance Criteria`

Checklist format preferred:

```markdown
## Acceptance Criteria

- [ ] Login button is visible
- [ ] OAuth flow completes successfully
- [ ] Session persists across page reloads
- [x] Unit tests written
```

### `## Technical Notes`

Implementation details, constraints, or technical considerations.

### `## Links`

Related resources in bullet format:

```markdown
## Links

- pr: #142
- issue: #77
- commit: a3f8d9c
- docs: https://docs.example.com/oauth
- related: LT-012
```

### Custom Sections

Any H2/H3 sections are allowed:

```markdown
## Testing Plan
## Security Considerations  
## Dependencies
## Questions
```

---

## Parsing Rules

### Title Line Parsing

```rust
// Pseudocode
regex: r"^# ([A-Z]+-\d+) — (.+)$"
// Capture groups:
// 1: Task ID (e.g., "LT-014")
// 2: Title (e.g., "Add OAuth login")
```

### Frontmatter Parsing

1. Read lines after title until first `##` heading
2. Skip empty lines
3. Parse as YAML document
4. Validate required fields present
5. Validate `status` matches config
6. Validate `priority` matches config (if present)
7. Parse dates in ISO 8601 format

**Error Handling**:
- Missing required field → Error with field name
- Invalid YAML → Error with line number
- Unknown status → Error with valid options
- Invalid date format → Error with expected format

### Body Parsing

1. Start at first `##` heading
2. Parse as CommonMark markdown
3. Preserve exact formatting (no reflow)
4. Preserve line endings

---

## Writing Rules

When lazytrack updates a task file:

### Preserve User Content

- Never rewrite unchanged sections
- Preserve whitespace in body
- Preserve markdown formatting
- Preserve comments in frontmatter
- Preserve unknown fields
- Preserve field order when possible

### Safe Updates

**Update Only Changed Fields**:

```yaml
# Before
status: in_progress
owner: nate

# After updating status to 'done'
status: done
owner: nate
```

**Don't Rewrite Entire File** unless necessary for:
- Format migration
- Explicit user request
- Normalization command

### Atomic Writes

1. Read existing file
2. Parse into structure
3. Apply changes
4. Write to temporary file (`.lazytrack/tasks/.tmp-<ID>.md`)
5. Atomic rename to final location
6. Delete temporary file

This ensures no data loss on write failure.

---

## Merge Safety

Task files are designed for git merge:

### Merge-Friendly Design

- One field per line in frontmatter
- Alphabetical field order (recommended)
- Body sections clearly delimited by headings
- No auto-generated content in body

### Conflict Resolution

On merge conflicts:

1. **Frontmatter conflicts**: Git shows both versions, user resolves
2. **Body conflicts**: Standard markdown merge (usually clean)
3. **Status conflicts**: User must choose (no auto-merge)

**Best Practice**: Use branches per task, merge frequently.

---

## Validation

lazytrack validates task files on load:

### File Level

- File name matches pattern `<PREFIX>-\d+\.md`
- File is UTF-8 encoded
- File size < 1MB (warning for large files)

### Structure Level

- Title line present and valid
- Frontmatter is valid YAML
- Required fields present

### Semantic Level

- Status exists in config
- Priority exists in config (if set)
- Dates are valid ISO 8601
- Referenced tasks exist (if checked)
- Linked commits exist in git (if checked)

**Validation Modes**:
- **Strict** (default): Error on any validation failure
- **Lenient**: Warn but continue on non-critical issues
- **None**: Skip validation (for scripts/tools)

---

## ID Assignment

Task IDs are assigned sequentially:

1. Read `task_counter` from `config.yaml`
2. Increment counter
3. Format as `<PREFIX>-<NUMBER>` (zero-padded to 3 digits)
4. Write back to config
5. Create task file

**Example Sequence**:
- `task_counter: 5` → creates `LT-005.md` → `task_counter: 6`
- `task_counter: 42` → creates `LT-042.md` → `task_counter: 43`
- `task_counter: 999` → creates `LT-999.md` → `task_counter: 1000`
- `task_counter: 1000` → creates `LT-1000.md` (no padding beyond 3 digits)

---

## Edge Cases

### Empty Task

Minimal valid task:

```markdown
# LT-001 — Empty task

status: todo
created: 2026-02-03
```

### Task With No Body

```markdown
# LT-002 — Quick fix

status: done
created: 2026-02-03
updated: 2026-02-03
```

### Task With Long Description

Body sections can be arbitrarily long. No length limit enforced.

### Unicode in Title

```markdown
# LT-003 — Fix emoji rendering 🎉

status: todo
created: 2026-02-03
```

Unicode fully supported in title and body.

---

## Backward Compatibility

### Version 0.x

Task file format may evolve. Changes will be documented in migration guides.

### Version 1.x

Task file format is stable. New optional fields may be added, but:
- Required fields will not change
- Existing fields will not be renamed
- Parsing will remain backward compatible

### Migration

On format changes, `lazytrack doctor --migrate` will:
1. Detect old format
2. Create backups
3. Migrate to new format
4. Validate migrated files

---

## Performance Considerations

### Large Repos

For repos with 1000+ tasks:
- Task file reads are lazy (on-demand)
- Index file caches metadata (future)
- Glob patterns for filtering
- Parallel file reads

### File Size

Recommended task file size: < 50KB  
Warning threshold: 500KB  
Maximum: 1MB (after which lazytrack may refuse to parse)

For large tasks, link to external docs rather than embedding.

---

## Examples

### Bug Report Task

```markdown
# LT-015 — Fix login redirect loop

status: todo
priority: critical
owner: alice
labels: [bug, auth, urgent]
created: 2026-02-05

## Description

Users are getting stuck in a redirect loop after OAuth login. The callback
handler seems to be triggering another OAuth flow instead of creating a session.

## Steps to Reproduce

1. Navigate to `/login`
2. Click "Login with GitHub"
3. Authorize the app
4. Observe infinite redirect loop

## Expected Behavior

User should be redirected to dashboard after successful OAuth callback.

## Actual Behavior

Browser gets stuck in redirect loop between `/auth/callback` and `/auth/github`.

## Links

- issue: #203
- related: LT-014
```

### Feature Task

```markdown
# LT-016 — Add dark mode toggle

status: in_progress
priority: medium
owner: nate
labels: [feature, ui, frontend]
milestone: v1.2.0
created: 2026-02-04
updated: 2026-02-06
branch: feature/dark-mode

## Description

Add a dark mode toggle to the settings page. Theme preference should be
persisted to localStorage and applied on page load.

## Acceptance Criteria

- [ ] Toggle switch on settings page
- [ ] Dark theme CSS variables defined
- [ ] Theme persisted to localStorage
- [ ] Smooth transition between themes
- [ ] Respects prefers-color-scheme media query

## Technical Notes

Use CSS custom properties for theming. Consider using `theme-ui` or
a similar library for consistent theme switching.

## Links

- pr: #156
- design: https://figma.com/dark-mode-mockup
```

### Completed Task

```markdown
# LT-017 — Update dependencies

status: done
priority: low
owner: bob
labels: [maintenance, dependencies]
milestone: v0.2.0
created: 2026-02-01
updated: 2026-02-03
commits: [f3e8a1c, d2b9e4f]
pr: 145

## Description

Update all npm dependencies to latest versions. Run tests to ensure
no breaking changes.

## Acceptance Criteria

- [x] Dependencies updated in package.json
- [x] Lockfile regenerated
- [x] All tests passing
- [x] No security vulnerabilities

## Links

- pr: #145
- commit: f3e8a1c
```

---

## See Also

- [Configuration Specification](./config-spec.md)
- [Workflow Rules Specification](./workflow-spec.md)
- [Directory Structure](./directory-structure-spec.md)
