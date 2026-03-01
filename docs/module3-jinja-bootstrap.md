---
title: "Module 3: The Face (Jinja2 & Bootstrap)"
layout: default
nav_order: 4
---

# Module 3: The Face (Jinja2 & Bootstrap)

**Goal:** Build a clean, simple UI that connects to the backend using templates and minimal Bootstrap styling.

---

## Learning Objectives

By the end of this day, you will be able to:

- Create and use Jinja2 templates
- Understand Template Inheritance with `base.html`
- Pass data from Flask routes to HTML
- Integrate Bootstrap 5 for simple, clean styling
- Use loops and conditionals in templates

---

## 1. Introducing Jinja2

Jinja2 is a templating engine for Python. It allows you to write HTML with "blanks" that Flask fills in with real data from your Python code.

### Syntax Cheatsheet

{% raw %}
| Syntax | Description | Example |
|--------|-------------|---------|
| `{{ variable }}` | Displays value of a variable | `{{ name }}` |
| `{% ... %}` | Control flow (loops, if/else) | `{% for task in tasks %}` |
| `{# ... #}` | Comments (hidden from browser) | `{# This is a comment #}` |
{% endraw %}

---

## 2. Template Inheritance

Instead of repeating the same navbar and footer on every page, we use a "parent" template.

### The Parent: `templates/base.html`

{% raw %}
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{% block title %}TaskMaster{% endblock %}</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <nav class="navbar navbar-dark bg-dark mb-3">
        <div class="container">
            <a class="navbar-brand" href="/">TaskMaster</a>
            <div>
                <a class="text-light me-2" href="/">Home</a>
                <a class="text-light" href="/tasks">All Tasks</a>
            </div>
        </div>
    </nav>

    <div class="container">
        {% block content %}{% endblock %}
    </div>
</body>
</html>
```
{% endraw %}

### The Child: `templates/index.html`

{% raw %}
```html
{% extends "base.html" %}

{% block title %}Dashboard - TaskMaster{% endblock %}

{% block content %}
<h1>Your Tasks</h1>

{% for task in tasks %}
<div class="card mb-3">
    <div class="card-body">
        <h5>{{ task.title }}</h5>
        <p>{{ task.description }}</p>
        <p>
            <span class="badge bg-secondary">{{ task.priority }}</span>
            <span class="badge bg-dark">{{ task.status }}</span>
        </p>
    </div>
</div>
{% endfor %}
{% endblock %}
```
{% endraw %}

---

## 3. Connecting Flask to Templates

Update your `app.py` to use `render_template`:

```python
from flask import Flask, render_template
from models import Task # Assuming models are in models.py

@app.route('/')
def index():
    # Fetch tasks from DB
    all_tasks = Task.query.all()
    # Pass them to the template
    return render_template('index.html', tasks=all_tasks)
```

---

## 4. Simple Bootstrap Components

### Basic Navbar

{% raw %}
```html
<nav class="navbar navbar-dark bg-dark mb-3">
    <div class="container">
        <a class="navbar-brand" href="/">TaskMaster</a>
        <div>
            <a class="text-light me-2" href="/">Home</a>
            {% if session.user_id %}
                <a class="text-light" href="/logout">Logout</a>
            {% else %}
                <a class="text-light" href="/login">Login</a>
            {% endif %}
        </div>
    </div>
</nav>
```
{% endraw %}

### Basic Form

{% raw %}
```html
<form method="POST">
    <div class="mb-3">
        <label>Title</label>
        <input type="text" name="title" class="form-control" required>
    </div>
    <button class="btn btn-primary">Submit</button>
</form>
```
{% endraw %}

---

## How This Relates to Your Final Project

| TaskMaster Concept | Placement Portal Implementation |
|--------------------|---------------------------------|
| `base.html` | Shared layout with Navigation, Logo and Logout button |
| Bootstrap Cards | Displaying individual Placement Drives or Company Profiles |
| Jinja2 `if` statements | Showing "Approve" button only to Admin |
| Bootstrap Tables | Listing all students applied for a specific drive |

---

## Key Takeaways

1. **`render_template`**: The function used to serve HTML files from the `templates/` folder.
2. **Template Inheritance**: Saves time by sharing layouts across multiple pages using `{% extends %}` and `{% block %}`.
3. **Bootstrap Basics**: Use Bootstrap for simple styling - navbar, buttons, forms, cards. Keep it minimal and easy to understand.
4. **Jinja2 Syntax**: 
   - `{{ variable }}` - Display data
   - `{% for %}` - Loop through lists
   - `{% if %}` - Conditional logic

---

[Previous: Module 2](module2-database.html){: .btn } [Next: Module 4](module4-auth.html){: .btn .btn-primary }
