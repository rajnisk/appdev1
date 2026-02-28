---
title: "Module 5: The Interaction (Forms & CRUD)"
layout: default
nav_order: 6
---

# Module 5: The Interaction (Forms & CRUD)

**Goal:** Complete the user lifecycle by handling form submissions and linking data through relationships.

---

## Learning Objectives

By the end of this day, you will be able to:

- Handle `POST` requests from HTML forms
- Perform full CRUD (Create, Read, Update, Delete) operations
- Link data using One-to-Many relationships
- Use Jinja2 to pre-fill forms for editing

---

## 1. Handling HTML Forms

In Flask, we use the `request` object to get data submitted by users.

### The HTML Form
```html
{% raw %}
<form action="/task/create" method="POST">
    <div class="mb-3">
        <label>Task Title</label>
        <input type="text" name="title" class="form-control" required>
    </div>
    <button type="submit" class="btn btn-success">Create Task</button>
</form>
{% endraw %}
```

### The Flask Route
```python
@app.route('/task/create', methods=['GET', 'POST'])
def create_task():
    if request.method == 'POST':
        title = request.form.get('title')
        description = request.form.get('description')
        
        # Link to current user
        new_task = Task(title=title, description=description, user_id=session['user_id'])
        db.session.add(new_task)
        db.session.commit()
        return redirect('/')
    
    return render_template('create_task.html')
```

---

## 2. The Relationship: One-to-Many

In your database, a User can have many Tasks. This is established using a `ForeignKey`.

| Feature | User Model | Task Model |
|---------|------------|------------|
| Code | `tasks = db.relationship('Task', ...)` | `user_id = db.ForeignKey('user.id')` |
| Meaning | "Give me all tasks for this user" | "This task belongs to exactly one user" |

### Why this matters?
In the **Placement Portal**, a `Company` has many `PlacementDrives`. A `Drive` has many `Applications`.

---

## 3. Editing and Updating

Updating data follows a specific pattern:
1. **Fetch** the existing object by ID.
2. **Display** its current values in a form.
3. **Save** the changes back to the database.

```python
@app.route('/task/edit/<int:id>', methods=['GET', 'POST'])
def edit_task(id):
    task = Task.query.get_or_404(id)
    
    if request.method == 'POST':
        task.title = request.form.get('title')
        task.status = request.form.get('status')
        db.session.commit()
        return redirect('/')
        
    return render_template('edit_task.html', task=task)
```

---

## 4. Deleting Data

```python
@app.route('/task/delete/<int:id>')
def delete_task(id):
    task = Task.query.get_or_404(id)
    db.session.delete(task)
    db.session.commit()
    return redirect('/')
```

---

## How This Relates to Your Final Project

| TaskMaster Concept | Placement Portal Implementation |
|--------------------|---------------------------------|
| Create Task | Company creating a new Placement Drive |
| Link Task to User | Linking a Drive to the Company that created it |
| Edit Task | Admin updating student details or status |
| Delete Task | Admin removing a student or blacklisting a company |

---

## Key Takeaways

1. **`request.form.get('name')`**: How to get data from an input field.
2. **`methods=['GET', 'POST']`**: Routes often need to handle both displaying and submitting forms.
3. **`db.session.commit()`**: Always required to persist changes (Create, Update, or Delete).
4. **`ForeignKey`**: The glue that connects different tables in your database.

---

[Previous: Module 4](module4-auth.html){: .btn } [Next: Module 6](module6-approval-flow.html){: .btn .btn-primary }
