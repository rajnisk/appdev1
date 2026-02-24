---
title: "Day 8: The Polish (Validation & Structure)"
layout: default
nav_order: 9
---

# Day 8: The Polish (Validation & Structure)

**Goal:** Make your application robust, user-friendly, and ready for submission.

---

## Learning Objectives

By the end of this day, you will be able to:

- Create custom error pages (404 and 500)
- Implement backend validation to prevent database errors
- Organize your code into a professional project structure
- Prepare your project for final zip submission

---

## 1. Custom Error Pages

A professional app shouldn't show the default "404 Not Found" browser page.

```python
@app.errorhandler(404)
def page_not_found(e):
    return render_template('404.html'), 404

@app.errorhandler(500)
def server_error(e):
    return render_template('500.html'), 500
```

---

## 2. Backend Validation

Never trust user input. Always check data before committing to the database.

```python
@app.route('/task/create', methods=['POST'])
def create_task():
    title = request.form.get('title')
    
    # Validation 1: Check for empty field
    if not title:
        flash("Title cannot be empty!", "danger")
        return redirect('/task/create')
        
    # Validation 2: Check for duplicates
    existing = Task.query.filter_by(title=title, user_id=session['user_id']).first()
    if existing:
        flash("You already have a task with this name!", "warning")
        return redirect('/task/create')
        
    # [Commit logic here...]
```

---

## 3. Project Organization

As your project grows, don't keep everything in one file. Move models and logic into separate files.

### Recommended Structure
```
taskmaster/
├── app.py           # Main entry point
├── models.py        # Database models
├── static/          # CSS, Images, JS
├── templates/       # HTML files
├── venv/            # Virtual environment
└── database.sqlite  # The DB file
```

---

## 4. Final Relatability (TaskMaster vs. Placement Portal)

| Skill | Final Project Context |
|-------|-----------------------|
| 404 Pages | Handling a student visiting a non-existent Drive ID |
| Validation | Ensuring a student can't apply to the same drive twice |
| Refactoring | Keeping your code clean for the Viva and project report |
| Zip Submission | Following the exact zipping instructions in the project doc |

---

## Key Takeaways

1. **`errorhandler`**: Captures errors and shows a branded page instead.
2. **Double Checks**: Always validate on the backend, even if you have HTML5 validation.
3. **Modularity**: Smaller files are easier to debug and explain during a demo.
4. **Readiness**: A polished app includes clear failure messages and a clean UI.

---

[Previous: Day 7](day7-search-api.html){: .btn } [Back to Roadmap](index.html){: .btn .btn-primary }
