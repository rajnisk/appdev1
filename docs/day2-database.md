---
title: "Day 2: The Foundation (SQLite & SQLAlchemy)"
layout: default
nav_order: 3
---

# Day 2: The Foundation (SQLite & SQLAlchemy)

**Goal:** Make data persist using a database.

---

## Learning Objectives

By the end of this day, you will be able to:

- Understand the difference between SQL and ORM
- Define database models using SQLAlchemy
- Initialize and create database tables
- Perform CRUD operations (Create, Read, Update, Delete)

---

## 1. Why Do We Need a Database?

Right now, our app shows static text. When we restart the server, any data is lost. A database:

- **Persists data** across server restarts
- **Organizes data** in structured tables
- **Enables queries** to find specific information
- **Handles relationships** between different data types

---

## 2. SQL vs ORM

### Raw SQL (Traditional Way)

```sql
CREATE TABLE tasks (
    id INTEGER PRIMARY KEY,
    title VARCHAR(100) NOT NULL,
    description TEXT,
    status VARCHAR(20) DEFAULT 'pending'
);

INSERT INTO tasks (title, description) VALUES ('Learn Flask', 'Complete day 1');
SELECT * FROM tasks WHERE status = 'pending';
```

### ORM - Object Relational Mapping (Modern Way)

With SQLAlchemy, we work with **Python objects** instead of SQL strings:

```python
# Define a model (like a table)
class Task(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), nullable=False)
    description = db.Column(db.Text)
    status = db.Column(db.String(20), default='pending')

# Create a new task
new_task = Task(title='Learn Flask', description='Complete day 1')
db.session.add(new_task)
db.session.commit()

# Query tasks
pending_tasks = Task.query.filter_by(status='pending').all()
```

{: .highlight }
**ORM Advantage:** Write Python code, not SQL. SQLAlchemy translates it for you.

---

## 3. Setting Up SQLAlchemy

### Install Flask-SQLAlchemy

```bash
pip install flask-sqlalchemy
```

### Configure Your App

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)

# Database Configuration
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///taskmaster.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

# Initialize SQLAlchemy
db = SQLAlchemy(app)
```

### Understanding the Config

| Config | Explanation |
|--------|-------------|
| `sqlite:///taskmaster.db` | Use SQLite, store in file `taskmaster.db` |
| `TRACK_MODIFICATIONS = False` | Disable event tracking (saves memory) |

{: .note }
The `instance/` folder will be created automatically to store your database file.

---

## 4. Defining Models

A **Model** is a Python class that represents a database table.

### The Task Model

```python
class Task(db.Model):
    # Table name (optional - defaults to class name in lowercase)
    __tablename__ = 'tasks'
    
    # Columns
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), nullable=False)
    description = db.Column(db.Text, nullable=True)
    status = db.Column(db.String(20), default='pending')
    priority = db.Column(db.String(10), default='medium')
    
    # String representation (for debugging)
    def __repr__(self):
        return f'<Task {self.id}: {self.title}>'
```

### Common Column Types

| SQLAlchemy Type | Python Type | Use Case |
|-----------------|-------------|----------|
| `db.Integer` | `int` | IDs, counts |
| `db.String(n)` | `str` | Short text (max n chars) |
| `db.Text` | `str` | Long text (unlimited) |
| `db.Boolean` | `bool` | True/False flags |
| `db.DateTime` | `datetime` | Dates and times |
| `db.Float` | `float` | Decimal numbers |

### Column Constraints

| Constraint | Meaning |
|------------|---------|
| `primary_key=True` | Unique identifier for each row |
| `nullable=False` | Field cannot be empty (required) |
| `default='value'` | Default value if not provided |
| `unique=True` | No duplicates allowed |

---

## 5. Creating the Database

### Method 1: Using App Context

```python
# At the bottom of app.py
if __name__ == '__main__':
    with app.app_context():
        db.create_all()  # Creates all tables
    app.run(debug=True)
```

### Method 2: Using Flask Shell

```bash
flask shell
```

```python
>>> from app import db
>>> db.create_all()
>>> exit()
```

{: .important }
`db.create_all()` only creates tables that don't exist. It won't modify existing tables.

---

## 6. CRUD Operations

### CREATE - Adding New Tasks

```python
# In Flask shell
>>> from app import db, Task

# Create a new task
>>> task1 = Task(title='Learn Flask', description='Complete day 1 tutorial', priority='high')
>>> db.session.add(task1)
>>> db.session.commit()

# Create multiple tasks
>>> task2 = Task(title='Setup Database', description='Configure SQLAlchemy')
>>> task3 = Task(title='Build UI', description='Create templates', priority='low')
>>> db.session.add_all([task2, task3])
>>> db.session.commit()
```

### READ - Querying Tasks

```python
# Get ALL tasks
>>> Task.query.all()
[<Task 1: Learn Flask>, <Task 2: Setup Database>, <Task 3: Build UI>]

# Get task by ID
>>> Task.query.get(1)
<Task 1: Learn Flask>

# Get FIRST matching task
>>> Task.query.filter_by(status='pending').first()
<Task 1: Learn Flask>

# Get ALL matching tasks
>>> Task.query.filter_by(priority='high').all()
[<Task 1: Learn Flask>]

# Filter with conditions
>>> Task.query.filter(Task.title.contains('Flask')).all()
[<Task 1: Learn Flask>]

# Order results
>>> Task.query.order_by(Task.id.desc()).all()  # Newest first

# Count results
>>> Task.query.filter_by(status='pending').count()
3
```

### UPDATE - Modifying Tasks

```python
# Get the task
>>> task = Task.query.get(1)

# Update fields
>>> task.status = 'in-progress'
>>> task.description = 'Updated description'

# Save changes
>>> db.session.commit()

# Verify
>>> Task.query.get(1).status
'in-progress'
```

### DELETE - Removing Tasks

```python
# Get the task
>>> task = Task.query.get(3)

# Delete it
>>> db.session.delete(task)
>>> db.session.commit()

# Verify it's gone
>>> Task.query.get(3)
None
```

---

## 7. Complete Day 2 App

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///taskmaster.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

db = SQLAlchemy(app)


# ==================== MODELS ====================

class Task(db.Model):
    __tablename__ = 'tasks'
    
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), nullable=False)
    description = db.Column(db.Text, nullable=True)
    status = db.Column(db.String(20), default='pending')
    priority = db.Column(db.String(10), default='medium')
    
    def __repr__(self):
        return f'<Task {self.id}: {self.title}>'
    
    def to_dict(self):
        return {
            'id': self.id,
            'title': self.title,
            'description': self.description,
            'status': self.status,
            'priority': self.priority
        }


# ==================== ROUTES ====================

@app.route('/')
def home():
    return """
    <h1>TaskMaster</h1>
    <nav>
        <a href="/tasks">View All Tasks</a> |
        <a href="/tasks/pending">Pending</a> |
        <a href="/tasks/completed">Completed</a>
    </nav>
    """

@app.route('/tasks')
def all_tasks():
    tasks = Task.query.all()
    
    if not tasks:
        return "<h1>No tasks yet!</h1><p>Add some tasks using Flask shell.</p>"
    
    html = "<h1>All Tasks</h1><ul>"
    for task in tasks:
        status_mark = "[DONE]" if task.status == 'completed' else "[PENDING]"
        html += f"<li>{status_mark} <a href='/task/{task.id}'>{task.title}</a> [{task.priority}]</li>"
    html += "</ul><a href='/'>Home</a>"
    
    return html

@app.route('/tasks/<status>')
def tasks_by_status(status):
    tasks = Task.query.filter_by(status=status).all()
    
    html = f"<h1>{status.title()} Tasks</h1>"
    
    if not tasks:
        html += f"<p>No {status} tasks found.</p>"
    else:
        html += "<ul>"
        for task in tasks:
            html += f"<li><a href='/task/{task.id}'>{task.title}</a></li>"
        html += "</ul>"
    
    html += "<a href='/tasks'>All Tasks</a>"
    return html

@app.route('/task/<int:task_id>')
def task_detail(task_id):
    task = Task.query.get(task_id)
    
    if not task:
        return "<h1>Task not found!</h1><a href='/tasks'>Back</a>", 404
    
    priority_color = {'high': 'red', 'medium': 'orange', 'low': 'green'}.get(task.priority, 'gray')
    
    return f"""
    <h1>{task.title}</h1>
    <p><strong>Description:</strong> {task.description or 'No description'}</p>
    <p><strong>Status:</strong> {task.status}</p>
    <p><strong>Priority:</strong> <span style="color:{priority_color}">{task.priority}</span></p>
    <hr>
    <a href="/tasks">Back to Tasks</a>
    """

@app.route('/priority/<level>')
def tasks_by_priority(level):
    tasks = Task.query.filter_by(priority=level).all()
    
    html = f"<h1>{level.title()} Priority Tasks</h1>"
    
    if not tasks:
        html += f"<p>No {level} priority tasks.</p>"
    else:
        html += "<ul>"
        for task in tasks:
            html += f"<li><a href='/task/{task.id}'>{task.title}</a> - {task.status}</li>"
        html += "</ul>"
    
    html += "<a href='/tasks'>All Tasks</a>"
    return html


# ==================== MAIN ====================

if __name__ == '__main__':
    with app.app_context():
        db.create_all()
    app.run(debug=True)
```

---

## 8. Populating with Sample Data

Run these commands in Flask shell to add sample data:

```bash
flask shell
```

```python
from app import db, Task

# Clear existing data (optional)
Task.query.delete()
db.session.commit()

# Add sample tasks
tasks = [
    Task(title='Complete Flask Tutorial', description='Finish Day 1 and Day 2', priority='high', status='in-progress'),
    Task(title='Setup Database', description='Configure SQLAlchemy with SQLite', priority='high', status='completed'),
    Task(title='Build Dashboard UI', description='Create task list with Bootstrap', priority='medium'),
    Task(title='Add User Authentication', description='Login and signup forms', priority='medium'),
    Task(title='Write Documentation', description='Document all routes and models', priority='low'),
    Task(title='Deploy to Server', description='Host on PythonAnywhere', priority='low'),
]

db.session.add_all(tasks)
db.session.commit()

print(f"Added {Task.query.count()} tasks!")
```

Now run the app and visit `/tasks` to see your data.

---

## Day 2 Exercises

1. **Add a Category Field:** Add a `category` column (work, personal, study) to the Task model

2. **Due Date:** Add a `due_date` column using `db.DateTime`

3. **Query Practice:**
   - Find all high priority tasks that are pending
   - Count completed tasks
   - Get the most recent task (hint: order by id descending, limit 1)

4. **Create a Stats Route:** Create `/stats` that shows:
   - Total tasks
   - Tasks by status (pending, in-progress, completed)
   - Tasks by priority

{: .important }
**Challenge:** After adding a new column, you'll need to delete the old database and recreate it, or use migrations (advanced topic).

---

## How This Relates to Your Final Project

| TaskMaster Model | Placement Portal Models |
|------------------|------------------------|
| `Task` | `Student`, `Company`, `PlacementDrive`, `Application` |
| `task.status` | `drive.status` (pending, approved, closed) |
| `task.priority` | `application.status` (applied, shortlisted, selected) |
| Filter by status | Filter drives by approval status |
| Query by ID | Get student/company/drive details |

### Example: Placement Portal Models Preview

```python
class Student(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), nullable=False)
    email = db.Column(db.String(120), unique=True)
    branch = db.Column(db.String(50))
    cgpa = db.Column(db.Float)

class Company(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), nullable=False)
    industry = db.Column(db.String(50))

class PlacementDrive(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100))
    company_id = db.Column(db.Integer, db.ForeignKey('company.id'))
    status = db.Column(db.String(20), default='pending')
```

---

## Key Takeaways

1. **SQLAlchemy** lets you work with databases using Python objects
2. **Models** are Python classes that represent database tables
3. **db.create_all()** creates tables based on your models
4. **CRUD Operations:**
   - Create: `db.session.add()` + `commit()`
   - Read: `Model.query.all()`, `.get()`, `.filter_by()`
   - Update: Modify object + `commit()`
   - Delete: `db.session.delete()` + `commit()`
5. **Always commit** after making changes

---

## Quick Reference

```python
# Create
task = Task(title='New Task')
db.session.add(task)
db.session.commit()

# Read
Task.query.all()                              # All tasks
Task.query.get(1)                             # By ID
Task.query.filter_by(status='pending').all()  # Filter
Task.query.first()                            # First result

# Update
task = Task.query.get(1)
task.status = 'completed'
db.session.commit()

# Delete
task = Task.query.get(1)
db.session.delete(task)
db.session.commit()
```

---

[Previous: Day 1](day1-flask-routing.html){: .btn } [Next: Day 3](day3-jinja-bootstrap.html){: .btn .btn-primary }
