# Task Templates

This directory contains example task templates for common workflows.

These templates can be copied to your project's `.lazytrack/templates/` directory and customized for your team's needs.

---

## Available Templates

### `config.yaml`
Full configuration file template showing all available options.

**Usage**: Copy to `.lazytrack/config.yaml` during project initialization.

---

### `workflow.yaml`
Standard workflow automation rules template.

**Usage**: Copy to `.lazytrack/workflow.yaml` to enable PR/commit automation.

---

### `task-basic.md`
Minimal task template for general-purpose tasks.

**Usage**: 
```bash
lazytrack new --template basic "Task title"
```

---

### `task-bug.md`
Bug report template with steps to reproduce, expected/actual behavior sections.

**Usage**: 
```bash
lazytrack new --template bug "Fix login redirect bug"
```

**Sections**:
- Description
- Steps to Reproduce
- Expected Behavior
- Actual Behavior
- Environment
- Links

---

### `task-feature.md`
Feature request template with user story and acceptance criteria.

**Usage**: 
```bash
lazytrack new --template feature "Add dark mode toggle"
```

**Sections**:
- Description
- User Story
- Acceptance Criteria
- Technical Notes
- Links

---

### `task-spike.md`
Research/spike template for time-boxed investigations.

**Usage**: 
```bash
lazytrack new --template spike "Research OAuth providers"
```

**Sections**:
- Description
- Goal
- Timeboxed Duration
- Research Questions
- Success Criteria
- Links

---

## Template Variables

Templates support the following variable substitutions:

| Variable | Replaced With | Example |
|----------|---------------|---------|
| `{{ID}}` | Task ID | `LT-042` |
| `{{TITLE}}` | Task title (from command) | `Fix auth bug` |
| `{{DATE}}` | Current date (ISO 8601) | `2026-02-04` |
| `{{OWNER}}` | Git user name | `nate` |
| `{{MILESTONE}}` | Current milestone | `v1.0.0` |

---

## Creating Custom Templates

1. Create a new `.md` file in `.lazytrack/templates/`
2. Use template variables where needed
3. Follow the [Task File Specification](../specs/task-file-spec.md)
4. Test with `lazytrack new --template <name> "Title"`

**Example Custom Template** (`.lazytrack/templates/refactor.md`):

```markdown
# {{ID}} — {{TITLE}}

status: todo
priority: low
owner: {{OWNER}}
labels: [refactor, tech-debt]
created: {{DATE}}

## Description

What code needs refactoring and why?

## Current Issues

- Issue 1
- Issue 2

## Proposed Improvement

How should the code be restructured?

## Benefits

- Benefit 1
- Benefit 2

## Links

- related:
```

**Usage**:
```bash
lazytrack new --template refactor "Simplify auth middleware"
```

---

## See Also

- [Task File Specification](../specs/task-file-spec.md)
- [Configuration Specification](../specs/config-spec.md)
- [Workflow Specification](../specs/workflow-spec.md)
