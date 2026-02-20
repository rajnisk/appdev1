---
title: "Day 4: The Gatekeeper (Auth & Roles)"
layout: default
nav_order: 5
---

# Day 4: The Gatekeeper (Auth & Roles)

**Goal:** Secure the application and isolate data between different users.

---

## Learning Objectives

By the end of this day, you will be able to:

- Implement password hashing with `werkzeug.security`
- Handle User Login and Signup logic
- Use `flask.session` to track logged-in users
- Establish relationships between Users and Tasks
- Protect routes from unauthorized access

---

## 1. Password Hashing

**NEVER store passwords as plain text.** We use "hashing" to store a mathematical thumbprint of the password instead.

### Using Werkzeug

```python
from werkzeug.security import generate_password_hash, check_password_hash

# When a user signs up
hashed_pw = generate_password_hash("my-secret-password")

# When a user logs in
is_correct = check_password_hash(hashed_pw, "typed-password")
```

---

## 2. Updated User & Task Models

We need to link each task to a specific user using a **One-to-Many Relationship**.

```python
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(50), unique=True, nullable=False)
    password_hash = db.Column(db.String(128))
    role = db.Column(db.String(20), default='Member') # e.g., 'Manager' or 'Member'

    # Relationship to tasks
    tasks = db.relationship('Task', backref='owner', lazy=True)

class Task(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), nullable=False)
    # Foreign Key linking to a User
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)
```

---

## 3. The Login Process

Flask uses `session` to remember a user across different page requests.

```python
from flask import session, redirect, request, flash

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        username = request.form.get('username')
        password = request.form.get('password')
        
        user = User.query.filter_by(username=username).first()
        
        if user and check_password_hash(user.password_hash, password):
            session['user_id'] = user.id
            session['role'] = user.role
            flash('Logged in successfully!', 'success')
            return redirect('/')
        else:
            flash('Invalid login details', 'danger')
            
    return render_template('login.html')
```

---

## 4. Protecting Routes

If someone isn't logged in, we shouldn't let them see the tasks.

```python
@app.route('/tasks')
def tasks():
    if 'user_id' not in session:
        flash("Please login first", "warning")
        return redirect('/login')
        
    # Find tasks belonging ONLY to this user
    user_tasks = Task.query.filter_by(user_id=session['user_id']).all()
    return render_template('tasks.html', tasks=user_tasks)
```

---

## 5. Logout

Logging out is as simple as clearing the session.

```python
@app.route('/logout')
def logout():
    session.clear()
    flash("You have been logged out", "info")
    return redirect('/login')
```

---

## How This Relates to Your Final Project

| TaskMaster Concept | Placement Portal Implementation |
|--------------------|---------------------------------|
| `session['role']` | Checking if current user is `Admin`, `Company`, or `Student` |
| Hashed Passwords | Secure login for all three user roles |
| `tasks = relationship(...)` | Linking `Applications` to `Students` and `Drives` |
| {% raw %}`filter_by(user_id=...)`{% endraw %} | Ensuring a student sees only their own application history |

---

## Key Takeaways

1. **Security**: Always hash passwords using `generate_password_hash`.
2. **Persistence**: `session` persists data between HTTP requests (which are normally stateless).
3. **Foreign Keys**: Used to create "Ownership" of data in the database.
4. **Data Isolation**: Always filter queries by the logged-in user's ID to prevent them from seeing other people's data.

---

[Previous: Day 3](day3-jinja-bootstrap.html){: .btn } [Next: Day 5](day5-interaction.html){: .btn .btn-primary }
