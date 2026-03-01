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
└── instance/database.sqlite     # (Generated automatically)
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

# --- API Route ---
@app.route('/api/tasks')
def api_tasks():
    if 'user_id' not in session:
        return jsonify({'error': 'Not authenticated'}), 401
    
    tasks = Task.query.filter_by(user_id=session['user_id']).all()
    tasks_list = []
    for task in tasks:
        tasks_list.append({
            'id': task.id,
            'title': task.title,
            'description': task.description,
            'status': task.status,
            'priority': task.priority
        })
    return jsonify(tasks_list)

# --- Error Handlers ---
@app.errorhandler(404)
def not_found(e):
    return render_template('404.html'), 404

@app.errorhandler(500)
def server_error(e):
    return render_template('500.html'), 500

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
<body>
    <nav class="navbar navbar-dark bg-dark mb-3">
        <div class="container">
            <a class="navbar-brand" href="/">TaskMaster</a>
            <div>
                {% if session.user_id %}
                    <span class="text-light me-3">Hi, {{ session.username }}</span>
                    <a class="text-light" href="/logout">Logout</a>
                {% else %}
                    <a class="text-light me-2" href="/login">Login</a>
                    <a class="text-light" href="/signup">Signup</a>
                {% endif %}
            </div>
        </div>
    </nav>

    <div class="container">
        <!-- Flash Messages -->
        {% with messages = get_flashed_messages(with_categories=true) %}
          {% if messages %}
            {% for cat, msg in messages %}
              <div class="alert alert-{{ cat }}">{{ msg }}</div>
            {% endfor %}
          {% endif %}
        {% endwith %}

        {% block content %}{% endblock %}
    </div>
</body>
</html>
```
{% endraw %}

### Dashboard: `templates/index.html`

{% raw %}
```html
{% extends "base.html" %}

{% block content %}
<h1>My Tasks</h1>
<a href="/task/create" class="btn btn-primary mb-3">+ New Task</a>

<form action="/" method="GET" class="mb-3">
    <input type="text" name="query" class="form-control d-inline-block" style="width: 300px;" placeholder="Search tasks...">
    <button class="btn btn-secondary">Search</button>
</form>

{% for task in tasks %}
<div class="card mb-3">
    <div class="card-body">
        <h5>{{ task.title }}</h5>
        <p>{{ task.description }}</p>
        <p>
            <span class="badge bg-secondary">{{ task.priority }}</span>
            <span class="badge bg-dark">{{ task.status }}</span>
        </p>
        <a href="/task/edit/{{ task.id }}" class="btn btn-sm btn-primary">Edit</a>
        <a href="/task/delete/{{ task.id }}" class="btn btn-sm btn-danger" onclick="return confirm('Delete this task?')">Delete</a>
    </div>
</div>
{% else %}
<p>No tasks found. Create one to get started!</p>
{% endfor %}
{% endblock %}
```
{% endraw %}

### Login: `templates/login.html`

{% raw %}
```html
{% extends "base.html" %}
{% block title %}Login{% endblock %}
{% block content %}
<h2>Login</h2>
<form method="POST">
    <div class="mb-3">
        <label>Username</label>
        <input type="text" name="username" class="form-control" required>
    </div>
    <div class="mb-3">
        <label>Password</label>
        <input type="password" name="password" class="form-control" required>
    </div>
    <button class="btn btn-primary">Login</button>
</form>
<p class="mt-3">Don't have an account? <a href="/signup">Sign up here</a></p>
{% endblock %}
```
{% endraw %}

### Signup: `templates/signup.html`

{% raw %}
```html
{% extends "base.html" %}
{% block title %}Signup{% endblock %}
{% block content %}
<h2>Create Account</h2>
<form method="POST">
    <div class="mb-3">
        <label>Username</label>
        <input type="text" name="username" class="form-control" required>
    </div>
    <div class="mb-3">
        <label>Password</label>
        <input type="password" name="password" class="form-control" required>
    </div>
    <button class="btn btn-success">Sign Up</button>
</form>
<p class="mt-3">Already have an account? <a href="/login">Login here</a></p>
{% endblock %}
```
{% endraw %}

### Create Task: `templates/create_task.html`

{% raw %}
```html
{% extends "base.html" %}
{% block title %}Create Task{% endblock %}
{% block content %}
<h2>Create New Task</h2>
<form method="POST">
    <div class="mb-3">
        <label>Title</label>
        <input type="text" name="title" class="form-control" required>
    </div>
    <div class="mb-3">
        <label>Description</label>
        <textarea name="description" class="form-control"></textarea>
    </div>
    <div class="mb-3">
        <label>Priority</label>
        <select name="priority" class="form-control">
            <option value="Low">Low</option>
            <option value="Medium" selected>Medium</option>
            <option value="High">High</option>
        </select>
    </div>
    <button class="btn btn-primary">Create Task</button>
    <a href="/" class="btn btn-secondary">Cancel</a>
</form>
{% endblock %}
```
{% endraw %}

### Edit Task: `templates/edit_task.html`

{% raw %}
```html
{% extends "base.html" %}
{% block title %}Edit Task{% endblock %}
{% block content %}
<h2>Edit Task</h2>
<form method="POST">
    <div class="mb-3">
        <label>Title</label>
        <input type="text" name="title" class="form-control" value="{{ task.title }}" required>
    </div>
    <div class="mb-3">
        <label>Description</label>
        <textarea name="description" class="form-control">{{ task.description }}</textarea>
    </div>
    <div class="mb-3">
        <label>Status</label>
        <select name="status" class="form-control">
            <option value="Pending" {% if task.status == 'Pending' %}selected{% endif %}>Pending</option>
            <option value="In Progress" {% if task.status == 'In Progress' %}selected{% endif %}>In Progress</option>
            <option value="Completed" {% if task.status == 'Completed' %}selected{% endif %}>Completed</option>
        </select>
    </div>
    <div class="mb-3">
        <label>Priority</label>
        <select name="priority" class="form-control">
            <option value="Low" {% if task.priority == 'Low' %}selected{% endif %}>Low</option>
            <option value="Medium" {% if task.priority == 'Medium' %}selected{% endif %}>Medium</option>
            <option value="High" {% if task.priority == 'High' %}selected{% endif %}>High</option>
        </select>
    </div>
    <button class="btn btn-primary">Update Task</button>
    <a href="/" class="btn btn-secondary">Cancel</a>
</form>
{% endblock %}
```
{% endraw %}

### Error 404: `templates/404.html`

{% raw %}
```html
{% extends "base.html" %}
{% block title %}404 - Not Found{% endblock %}
{% block content %}
<h1>404 - Page Not Found</h1>
<p>The page you're looking for doesn't exist.</p>
<a href="/" class="btn btn-primary">Go Home</a>
{% endblock %}
```
{% endraw %}

### Error 500: `templates/500.html`

{% raw %}
```html
{% extends "base.html" %}
{% block title %}500 - Server Error{% endblock %}
{% block content %}
<h1>500 - Server Error</h1>
<p>Something went wrong on our end. Please try again later.</p>
<a href="/" class="btn btn-primary">Go Home</a>
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

[Previous: Module 8](module8-polish.html){: .btn } [Back to Roadmap](index.html){: .btn .btn-primary }
