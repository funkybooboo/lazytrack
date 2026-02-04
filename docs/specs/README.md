# lazytrack Specifications

This directory contains technical specifications for lazytrack's file formats, configuration, and behavior.

---

## Core Specifications

### [Task File Specification](./task-file-spec.md)
Complete specification for task markdown files including:
- File naming conventions
- Frontmatter format and required fields
- Markdown body structure
- Parsing and validation rules
- Examples and edge cases

**Read this first** to understand how tasks are stored.

---

### [Configuration Specification](./config-spec.md)
Comprehensive guide to the `config.yaml` file including:
- All configuration options
- Default values
- Validation rules
- Examples for different project types
- Environment variable overrides

---

### [Workflow Rules Specification](./workflow-spec.md)
Workflow automation system documentation:
- Event types (git and PR events)
- Rule syntax and conditions
- Actions and field updates
- Conflict resolution
- Examples and debugging

---

### [Directory Structure Specification](./directory-structure-spec.md)
File organization and layout:
- Complete `.lazytrack/` directory structure
- File locations and purposes
- Git tracking recommendations
- Performance considerations
- Multi-user scenarios

---

## Quick Reference

### Minimal Valid Task

```markdown
# LT-001 — Task title

status: todo
created: 2026-02-04
```

### Minimal Config

```yaml
project:
  name: "MyProject"
  prefix: "MP"

task_counter: 1
```

### Basic Workflow

```yaml
rules:
  - when: pr_merged
    set_status: done
```

---

## Implementation Order

For building lazytrack, implement specs in this order:

1. **Directory Structure** — Understand file organization first
2. **Configuration** — Parse and validate config.yaml
3. **Task Files** — Parse and serialize task markdown files
4. **Workflow Rules** — Add automation (optional for MVP)

---

## Version Compatibility

**Current Version**: v0.x (pre-release)

These specifications may change during v0.x development. Breaking changes will be documented in migration guides.

Starting with **v1.0.0**, specifications become stable with semantic versioning guarantees.

---

## Contributing

To propose changes to specifications:

1. Open an issue describing the use case
2. Discuss the change with maintainers
3. Submit a PR updating the relevant spec document
4. Update examples and templates to match

See [CONTRIBUTING.md](../../CONTRIBUTING.md) for details.

---

## See Also

- [Roadmap](../../plans/roadmap.md) — Development timeline
- [Templates](../templates/) — Example configuration and task templates
- [README](../../README.md) — Project overview
