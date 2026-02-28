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
- Use SQLAlchemy filters like `like()` and `contains()`
- Return JSON data using `jsonify()`
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
    # Filter tasks where the title contains the search query
    results = Task.query.filter(Task.title.contains(search_query)).all()
    return render_template('index.html', tasks=results)
```

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

## 3. Filters vs. Parameters

| Feature | SQLAlchemy Equivalent |
|---------|-----------------------|
| Exact Match | `filter_by(status='Pending')` |
| Text Search | `filter(Task.title.contains('flask'))` |
| Multiple Filters | `filter_by(status='Done', priority='High')` |

---

## How This Relates to Your Final Project

| TaskMaster Concept | Placement Portal Implementation |
|--------------------|---------------------------------|
| Search Bar | Admin searching for students by name or enrollment ID |
| `contains()` | Finding companies based on partial names |
| `jsonify` | Creating an API for placement drives (required functionality) |
| Filtering | Student viewing only "Shortlisted" application history |

---

## Key Takeaways

1. **`request.args.get()`**: How to get data from a `GET` request (URL parameters).
2. **Text Filtering**: `Model.column.contains()` is the simplest way to add search.
3. **`jsonify`**: Turns Python lists/dicts into JSON format automatically.
4. **Machine-Friendly**: APIs are the bridge for future mobile apps or dashboards.

---

[Previous: Module 6](module6-approval-flow.html){: .btn } [Next: Module 8](module8-polish.md){: .btn .btn-primary }
