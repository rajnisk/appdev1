---
title: "Module 6: The Approval Flow (State Management)"
layout: default
nav_order: 7
---

# Module 6: The Approval Flow (State Management)

**Goal:** Implement business logic and provide visual feedback for state changes.

---

## Learning Objectives

By the end of this day, you will be able to:

- Implement multi-step workflows (e.g., Pending -> Approved)
- Use `flash()` messages for user notifications
- Hide or show buttons based on object status
- Update specific fields without full forms

---

## 1. Multi-Step Workflows

Most applications aren't just one-click. They involve a "request" and an "approval".

### The Status Cycle
In TaskMaster, a task might go: **Pending** -> **In Progress** -> **Completed**.

In the **Placement Portal**, a Placement Drive goes: **Requested (Company)** -> **Approved (Admin)** -> **Closed**.

### Updating Status via Buttons
Instead of a full edit page, we can use simple action buttons:

```python
{% raw %}
@app.route('/task/complete/<int:id>')
def complete_task(id):
    task = Task.query.get_or_404(id)
    task.status = 'Completed'
    db.session.commit()
    flash("Task marked as completed!", "success")
    return redirect('/')
{% endraw %}
```

---

## 2. Flash Messages

Flash messages are a way to send "one-time" notifications to the user (e.g., "Login Successful" or "Task Deleted").

### Step 1: Flash in Python
```python
from flask import flash
flash("Changes saved!", "success")
```

### Step 2: Show in HTML (`base.html`)
Bootstrap provides beautiful "Alert" components for this.

```html
{% raw %}
<div class="container mt-3">
    {% with messages = get_flashed_messages(with_categories=true) %}
        {% if messages %}
            {% for category, message in messages %}
                <div class="alert alert-{{ category }} alert-dismissible fade show">
                    {{ message }}
                    <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
                </div>
            {% endfor %}
        {% endif %}
    {% endwith %}
</div>
{% endraw %}
```

---

## 3. Conditional UI (Logic based on Status)

We should only show the "Complete" button if the task is NOT already finished.

```html
{% raw %}
{% if task.status != 'Completed' %}
    <a href="/task/complete/{{ task.id }}" class="btn btn-sm btn-outline-success">
        Mark Done
    </a>
{% else %}
    <span class="badge bg-success">Finished</span>
{% endif %}
{% endraw %}
```

---

## How This Relates to Your Final Project

| TaskMaster Concept | Placement Portal Implementation |
|--------------------|---------------------------------|
| `flash()` messages | Notifying student they successfully applied for a drive |
| Status Change | Admin clicking "Approve" on a new Placement Drive request |
| Conditional Buttons | Only showing "Apply" button to students if drive is `Approved` |
| State Management | Tracking if a student is `Shortlisted`, `Selected`, or `Rejected` |

---

## Key Takeaways

1. **`flash(message, category)`**: `category` (like 'success' or 'danger') maps directly to Bootstrap colors.
2. **State Logic**: A single field (`status`) can control the entire behavior of a page.
3. **Implicit Actions**: Sometimes a simple link/URL is better than a full form for updating state.
4. **User Feedback**: Always tell the user what happened (Success/Error).

---

[Previous: Module 5](module5-interaction.html){: .btn } [Next: Module 7](module7-search-api.md){: .btn .btn-primary }
