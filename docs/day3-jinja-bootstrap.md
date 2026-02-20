---
title: "Day 3: The Face (Jinja2 & Bootstrap)"
layout: default
nav_order: 4
---

# Day 3: The Face (Jinja2 & Bootstrap)

**Goal:** Build a professional UI that connects to the backend using templates and modern styling.

---

## Learning Objectives

By the end of this day, you will be able to:

- Create and use Jinja2 templates
- Understand Template Inheritance with `base.html`
- Pass data from Flask routes to HTML
- Integrate Bootstrap 5 for professional styling
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
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}TaskMaster{% endblock %}</title>
    <!-- Bootstrap 5 CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark mb-4">
        <div class="container">
            <a class="navbar-brand" href="/">TaskMaster</a>
            <div class="navbar-nav">
                <a class="nav-link" href="/">Home</a>
                <a class="nav-link" href="/tasks">All Tasks</a>
            </div>
        </div>
    </nav>

    <div class="container">
        {% block content %}{% endblock %}
    </div>

    <footer class="text-center mt-5 py-3 border-top">
        <p>&copy; 2024 MAD 1 Bootcamp</p>
    </footer>

    <!-- Bootstrap JS -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
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
    <div class="row align-items-center mb-4">
        <div class="col">
            <h1>Your Tasks</h1>
        </div>
        <div class="col text-end">
            <button class="btn btn-primary">+ Add New Task</button>
        </div>
    </div>

    <div class="row">
        {% for task in tasks %}
        <div class="col-md-4 mb-3">
            <div class="card h-100 shadow-sm border-{{ 'success' if task.status == 'Done' else 'warning' }}">
                <div class="card-body">
                    <h5 class="card-title">{{ task.title }}</h5>
                    <p class="card-text text-muted">{{ task.description }}</p>
                    <span class="badge bg-{{ 'secondary' if task.priority == 'Low' else 'info' if task.priority == 'Medium' else 'danger' }}">
                        {{ task.priority }}
                    </span>
                </div>
                <div class="card-footer bg-transparent">
                    <small class="text-muted">Status: {{ task.status }}</small>
                </div>
            </div>
        </div>
        {% endfor %}
    </div>
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

## 4. Key Bootstrap Component: The Navbar

Bootstrap's Navbar is essential for role-based navigation.

{% raw %}
```html
<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">Navbar</a>
    <!-- Toggler for mobile view -->
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarNav">
      <ul class="navbar-nav">
        <li class="nav-item">
          <a class="nav-link active" href="#">Dashboard</a>
        </li>
        <!-- Logic for showing links based on role -->
        {% if user_role == 'Admin' %}
        <li class="nav-item">
          <a class="nav-link" href="/admin">Manage Users</a>
        </li>
        {% endif %}
      </ul>
    </div>
  </div>
</nav>
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
2. **Template Inheritance**: Saves time by sharing layouts across multiple pages.
3. **Bootstrap CDN**: The fastest way to add CSS without downloading files.
4. **Conditional Styling**: Using {% raw %}`{{ 'text-success' if ... }}`{% endraw %} to change colors based on data.

---

[Previous: Day 2](day2-database.html){: .btn } [Next: Day 4](day4-auth.html){: .btn .btn-primary }
