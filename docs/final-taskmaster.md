---
title: "The Final Act: Full TaskMaster Code"
layout: default
nav_order: 10
---

# The Final Act: Complete TaskMaster Application

This page provides the complete, structured code for the TaskMaster application. We have organized the code following the **Model-View-Controller (MVC)** pattern to keep it professional and maintainable.

---

## 1. Project Folder Structure

Copy this structure exactly. Create the folders first, then the files.

```text
taskmaster/
├── app.py              # Controller: Routes & Application Logic
├── models.py           # Model: Database Schema
├── static/
│   └── css/
│       └── style.css   # Custom Styles (Optional)
├── templates/          # View: HTML Templates
│   ├── base.html       # Parent Layout
│   ├── index.html      # Dashboard
│   ├── login.html      # Login Page
│   ├── signup.html     # Signup Page
│   ├── create_task.html # Add Task Form
│   ├── edit_task.html   # Update Task Form
│   ├── 404.html        # Error Page
│   └── 500.html        # Error Page
└── database.sqlite     # (Generated automatically)
```

---

## 2. The Model: `models.py`

This file defines our database tables and the relationships between them.

```python
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(50), unique=True, nullable=False)
    password_hash = db.Column(db.String(128), nullable=False)
    role = db.Column(db.String(20), default='Member')
    
    # One-to-Many: One user has many tasks
    tasks = db.relationship('Task', backref='owner', lazy=True)

class Task(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), nullable=False)
    description = db.Column(db.Text)
    status = db.Column(db.String(20), default='Pending')
    priority = db.Column(db.String(20), default='Medium')
    
    # Foreign Key linking to User
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)
```

---

## 3. The Controller: `app.py`

This is the heart of the app. it handles configuration, routing, and business logic.

```python
from flask import Flask, render_template, request, redirect, session, flash, jsonify
from werkzeug.security import generate_password_hash, check_password_hash
from models import db, User, Task

app = Flask(__name__)
app.secret_key = 'your_secret_key_here'
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///database.sqlite'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

db.init_app(app)

# Initialize Database
with app.app_context():
    db.create_all()

# --- Auth Routes ---

@app.route('/signup', methods=['GET', 'POST'])
def signup():
    if request.method == 'POST':
        username = request.form.get('username')
        password = request.form.get('password')
        
        if User.query.filter_by(username=username).first():
            flash('Username already exists', 'danger')
            return redirect('/signup')
            
        new_user = User(username=username, password_hash=generate_password_hash(password))
        db.session.add(new_user)
        db.session.commit()
        flash('Account created! Please login.', 'success')
        return redirect('/login')
    return render_template('signup.html')

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        username = request.form.get('username')
        password = request.form.get('password')
        user = User.query.filter_by(username=username).first()
        
        if user and check_password_hash(user.password_hash, password):
            session['user_id'] = user.id
            session['username'] = user.username
            flash(f'Welcome back, {user.username}!', 'success')
            return redirect('/')
        flash('Invalid credentials', 'danger')
    return render_template('login.html')

@app.route('/logout')
def logout():
    session.clear()
    return redirect('/login')

# --- Task Routes (CRUD) ---

@app.route('/')
def index():
    if 'user_id' not in session:
        return redirect('/login')
    
    search_query = request.args.get('query')
    if search_query:
        tasks = Task.query.filter(Task.user_id == session['user_id'], 
                                  Task.title.contains(search_query)).all()
    else:
        tasks = Task.query.filter_by(user_id=session['user_id']).all()
        
    return render_template('index.html', tasks=tasks)

@app.route('/task/create', methods=['GET', 'POST'])
def create_task():
    if 'user_id' not in session: return redirect('/login')
    
    if request.method == 'POST':
        title = request.form.get('title')
        if not title:
            flash("Title is required!", "warning")
            return redirect('/task/create')
            
        new_task = Task(title=title, 
                        description=request.form.get('description'),
                        priority=request.form.get('priority'),
                        user_id=session['user_id'])
        db.session.add(new_task)
        db.session.commit()
        flash("Task added!", "success")
        return redirect('/')
    return render_template('create_task.html')

@app.route('/task/edit/<int:id>', methods=['GET', 'POST'])
def edit_task(id):
    task = Task.query.get_or_404(id)
    if task.user_id != session['user_id']: return redirect('/')
    
    if request.method == 'POST':
        task.title = request.form.get('title')
        task.status = request.form.get('status')
        task.priority = request.form.get('priority')
        db.session.commit()
        flash("Task updated!", "info")
        return redirect('/')
    return render_template('edit_task.html', task=task)

@app.route('/task/delete/<int:id>')
def delete_task(id):
    task = Task.query.get_or_404(id)
    if task.user_id == session['user_id']:
        db.session.delete(task)
        db.session.commit()
        flash("Task removed", "secondary")
    return redirect('/')

# --- Error Handlers ---
@app.errorhandler(404)
def not_found(e):
    return render_template('404.html'), 404

if __name__ == '__main__':
    app.run(debug=True)
```

---

## 4. The View: HTML Templates

### Parent Layout: `templates/base.html`

{% raw %}
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{% block title %}TaskMaster{% endblock %}</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark mb-4">
        <div class="container">
            <a class="navbar-brand" href="/">TaskMaster</a>
            <div class="navbar-nav ms-auto">
                {% if session.user_id %}
                    <span class="nav-link text-light">Hi, {{ session.username }}</span>
                    <a class="nav-link" href="/logout">Logout</a>
                {% else %}
                    <a class="nav-link" href="/login">Login</a>
                    <a class="nav-link" href="/signup">Signup</a>
                {% endif %}
            </div>
        </div>
    </nav>

    <div class="container">
        <!-- Flash Messages -->
        {% with messages = get_flashed_messages(with_categories=true) %}
          {% if messages %}
            {% for cat, msg in messages %}
              <div class="alert alert-{{ cat }} alert-dismissible fade show">{{ msg }}
                <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
              </div>
            {% endfor %}
          {% endif %}
        {% endwith %}

        {% block content %}{% endblock %}
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```
{% endraw %}

### Dashboard: `templates/index.html`

{% raw %}
```html
{% extends "base.html" %}

{% block content %}
<div class="d-flex justify-content-between align-items-center mb-4">
    <h1>My Tasks</h1>
    <a href="/task/create" class="btn btn-primary">+ New Task</a>
</div>

<form action="/" method="GET" class="d-flex mb-4">
    <input type="text" name="query" class="form-control me-2" placeholder="Search tasks...">
    <button class="btn btn-outline-secondary">Search</button>
</form>

<div class="row">
    {% for task in tasks %}
    <div class="col-md-4 mb-3">
        <div class="card h-100 shadow-sm border-{{ 'success' if task.status == 'Completed' else 'warning' }}">
            <div class="card-body">
                <h5 class="card-title">{{ task.title }}</h5>
                <p class="card-text text-muted">{{ task.description }}</p>
                <span class="badge bg-info">{{ task.priority }}</span>
                <span class="badge bg-dark">{{ task.status }}</span>
            </div>
            <div class="card-footer bg-transparent border-0 d-flex justify-content-between">
                <a href="/task/edit/{{ task.id }}" class="btn btn-sm btn-outline-primary">Edit</a>
                <a href="/task/delete/{{ task.id }}" class="btn btn-sm btn-outline-danger" onclick="return confirm('Delete this task?')">Delete</a>
            </div>
        </div>
    </div>
    {% else %}
    <p class="text-center text-muted">No tasks found. Create one to get started!</p>
    {% endfor %}
</div>
{% endblock %}
```
{% endraw %}

### Signup: `templates/signup.html`

{% raw %}
```html
{% extends "base.html" %}
{% block title %}Signup{% endblock %}
{% block content %}
<div class="row justify-content-center">
    <div class="col-md-4 mt-5">
        <div class="card shadow">
            <div class="card-body">
                <h3 class="text-center mb-4">Create Account</h3>
                <form method="POST">
                    <div class="mb-3"><label>Username</label><input type="text" name="username" class="form-control" required></div>
                    <div class="mb-3"><label>Password</label><input type="password" name="password" class="form-control" required></div>
                    <button class="btn btn-success w-100">Sign Up</button>
                </form>
                <p class="mt-3 text-center">Have an account? <a href="/login">Login</a></p>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```
{% endraw %}

---

## 5. How to Run

1. **Setup**: Create a folder named `taskmaster`.
2. **Install**: Run `pip install flask flask-sqlalchemy`.
3. **Files**: Create all files mentioned above inside the folder.
4. **Execution**: Run `python app.py`.
5. **Browsing**: Go to `http://127.0.0.1:5000` in your web browser.

---

[Previous: Day 8](day8-polish.html){: .btn } [Back to Roadmap](index.html){: .btn .btn-primary }
