---
title: "Module 7: The Bridge (Search & API Basics)"
layout: default
nav_order: 8
---

# Module 7: The Bridge (Search & API Basics)

**Goal:** Allow users to find data efficiently and expose raw data for external use.

---

## Learning Objectives

By the end of this day, you will be able to:

- Implement basic search functionality in Flask
- Use SQLAlchemy filters with LIKE pattern matching
- Return JSON data using `jsonify()`
- Create and display graphs/charts using matplotlib
- Understand the difference between SSR and API endpoints

---

## 1. Search Functionality

Search is essentially a dynamic filter on your database query.

### The Search Form
```html
{% raw %}
<form action="/search" method="GET" class="d-flex mb-4">
    <input type="text" name="query" class="form-control me-2" placeholder="Search tasks...">
    <button type="submit" class="btn btn-outline-primary">Search</button>
</form>
{% endraw %}
```
*Note: We use `GET` for search so the URL can be bookmarked/shared.*

### The Flask Logic
```python
@app.route('/search')
def search():
    search_query = request.args.get('query')
    if search_query:
        # Use LIKE pattern matching for flexible search
        # %query% means: match anywhere in the text
        results = Task.query.filter(Task.title.like(f'%{search_query}%')).all()
    else:
        results = Task.query.all()
    return render_template('index.html', tasks=results)
```

**Key Points:**
- `LIKE '%query%'` - Matches text anywhere (before, middle, or after)
- `LIKE 'query%'` - Matches text starting with "query"
- `LIKE '%query'` - Matches text ending with "query"
- Use `%` wildcards for flexible pattern matching

---

## 2. Returning JSON (The API)

While `render_template` returns HTML for humans, `jsonify` returns raw data for machines.

```python
from flask import jsonify

@app.route('/api/tasks')
def api_tasks():
    tasks = Task.query.all()
    # Convert list of objects to a list of dictionaries
    tasks_list = []
    for t in tasks:
        tasks_list.append({
            "id": t.id,
            "title": t.title,
            "status": t.status
        })
    return jsonify(tasks_list)
```

---

## 3. Creating Graphs and Charts

Sometimes you want to visualize data. We can create graphs using `matplotlib` and display them in templates.

### Installing matplotlib

```bash
pip install matplotlib
```

### Creating a Task Distribution Chart

Let's create a chart showing how many tasks each user has:

```python
from flask import render_template
import matplotlib
matplotlib.use('Agg')  # Use non-interactive backend
import matplotlib.pyplot as plt
import base64
from io import BytesIO
from models import User, Task

@app.route('/stats')
def stats():
    # Get all users and count their tasks
    users = User.query.all()
    user_names = []
    task_counts = []
    
    for user in users:
        user_names.append(user.username)
        task_counts.append(len(user.tasks))
    
    # Create the plot
    plt.figure(figsize=(8, 5))
    plt.bar(user_names, task_counts, color='skyblue')
    plt.xlabel('Users')
    plt.ylabel('Number of Tasks')
    plt.title('Task Distribution by User')
    plt.tight_layout()
    
    # Convert plot to base64 image
    img_buffer = BytesIO()
    plt.savefig(img_buffer, format='png')
    img_buffer.seek(0)
    img_base64 = base64.b64encode(img_buffer.getvalue()).decode()
    plt.close()
    
    # Pass to template
    return render_template('stats.html', chart_image=img_base64)
```

### Displaying the Chart in Template

Create `templates/stats.html`:

{% raw %}
```html
{% extends "base.html" %}
{% block title %}Statistics{% endblock %}
{% block content %}
<h2>Task Statistics</h2>
<img src="data:image/png;base64,{{ chart_image }}" alt="Task Distribution Chart">
{% endblock %}
```
{% endraw %}

### Placement Portal Example: Drive Applications Chart

```python
@app.route('/admin/dashboard')
def admin_dashboard():
    # Get all drives and count applications
    drives = PlacementDrive.query.all()
    drive_titles = []
    application_counts = []
    
    for drive in drives:
        drive_titles.append(drive.title[:20] + '...' if len(drive.title) > 20 else drive.title)
        application_counts.append(len(drive.applications))
    
    # Create bar chart
    plt.figure(figsize=(10, 6))
    plt.barh(drive_titles, application_counts, color='green')
    plt.xlabel('Number of Applications')
    plt.ylabel('Placement Drives')
    plt.title('Applications per Drive')
    plt.tight_layout()
    
    # Convert to base64
    img_buffer = BytesIO()
    plt.savefig(img_buffer, format='png')
    img_buffer.seek(0)
    img_base64 = base64.b64encode(img_buffer.getvalue()).decode()
    plt.close()
    
    return render_template('admin_dashboard.html', chart_image=img_base64)
```

**Key Points:**
- Use `matplotlib.use('Agg')` before importing pyplot (for server environments)
- Create plot with `plt.figure()` and plotting functions
- Save to BytesIO buffer, then encode to base64
- Always call `plt.close()` to free memory
- Display in template using `data:image/png;base64,{{ chart_image }}`

---

## 4. Filters vs. Parameters

| Feature | SQLAlchemy Equivalent |
|---------|-----------------------|
| Exact Match | `filter_by(status='Pending')` |
| Text Search (LIKE) | `filter(Task.title.like('%flask%'))` |
| Starts With | `filter(Task.title.like('flask%'))` |
| Ends With | `filter(Task.title.like('%tutorial'))` |
| Multiple Filters | `filter_by(status='Done', priority='High')` |

---

## How This Relates to Your Final Project

| TaskMaster Concept | Placement Portal Implementation |
|--------------------|---------------------------------|
| Search Bar | Admin searching for students by name or enrollment ID |
| `LIKE '%name%'` | Finding companies/students based on partial names |
| `jsonify` | Creating an API for placement drives (required functionality) |
| Filtering | Student viewing only "Shortlisted" application history |
| Charts/Graphs | Admin dashboard showing applications per drive, student statistics |

---

## Key Takeaways

1. **`request.args.get()`**: How to get data from a `GET` request (URL parameters).
2. **Text Filtering**: Use `LIKE '%query%'` for flexible pattern matching in search.
3. **`jsonify`**: Turns Python lists/dicts into JSON format automatically.
4. **Graphs**: Use matplotlib to create charts, convert to base64, and display in templates.
5. **Machine-Friendly**: APIs are the bridge for future mobile apps or dashboards.

---

[Previous: Module 6](module6-approval-flow.html){: .btn } [Next: Module 8](module8-polish.md){: .btn .btn-primary }
