# Workflow Rules Specification — lazytrack

Workflow rules automate task state changes based on git and PR events.

Stored at:

.lazytrack/workflow.yaml

---

# Example

rules:

  - when: branch_created
    set_status: in_progress

  - when: pr_opened
    set_status: review

  - when: pr_merged
    set_status: done

  - when: commit_contains_task_id
    add_link: commit

---

# Supported Events (MVP)

branch_created
branch_deleted
commit_detected
pr_opened
pr_closed
pr_merged

---

# Conditions

Rules may include filters:

- when: pr_opened
  if:
    label: feature
  set_status: review

---

# Conflict Resolution

If multiple rules match:

- apply in file order
- last rule wins

---

# Safety Rules

Automation must never:

- delete tasks
- overwrite user text sections
- remove unknown fields
