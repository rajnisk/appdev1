---
title: "Module 2: The Foundation (SQLite & SQLAlchemy)"
layout: default
nav_order: 3
---

# Module 2: The Foundation (SQLite & SQLAlchemy)

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

<!-- ### Method 2: Using Flask Shell

```bash
flask shell
```

```python
>>> from app import db
>>> db.create_all()
>>> exit()
``` -->

{: .important }
`db.create_all()` only creates tables that don't exist. It won't modify existing tables.

---

## 6. CRUD Operations

### Understanding CRUD Operations

CRUD stands for **Create, Read, Update, Delete** - the four basic operations for working with data.

<!-- ### CREATE - Adding New Tasks (Flask Shell Example)

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
``` -->

### CREATE - Adding New Tasks (Route Example)

Here's how to create a task using a Flask route:

```python
from flask import Flask, request, redirect

@app.route('/task/create', methods=['GET', 'POST'])
def create_task():
    if request.method == 'POST':
        # Get form data
        title = request.form.get('title')
        description = request.form.get('description')
        priority = request.form.get('priority', 'medium')
        
        # Create new task object
        new_task = Task(title=title, description=description, priority=priority)
        
        # Add to database session
        db.session.add(new_task)
        
        # Commit to save to database
        db.session.commit()
        
        return redirect('/tasks')
    
    return "Create Task Form Here"  # We'll build the form in Module 5
```

**Key Points:**
- `Task(...)` - Creates a new Task object
- `db.session.add(new_task)` - Adds it to the session
- `db.session.commit()` - Saves it to the database

### READ - Querying Tasks

Here are the common query methods (used in routes):

```python
# Get ALL tasks
tasks = Task.query.all()

# Get task by ID
task = Task.query.get(1)

# Get FIRST matching task
task = Task.query.filter_by(status='pending').first()

# Get ALL matching tasks
tasks = Task.query.filter_by(priority='high').all()

# Filter with conditions
tasks = Task.query.filter(Task.title.contains('Flask')).all()

# Order results
tasks = Task.query.order_by(Task.id.desc()).all()  # Newest first

# Count results
count = Task.query.filter_by(status='pending').count()
```

<!-- ### UPDATE - Modifying Tasks (Flask Shell Example)

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
``` -->

### UPDATE - Modifying Tasks (Route Example)

Here's how to update a task using a Flask route:

```python
@app.route('/task/update/<int:task_id>', methods=['POST'])
def update_task(task_id):
    # Get the task from database
    task = Task.query.get(task_id)
    
    if not task:
        return "Task not found", 404
    
    # Update fields from form data
    task.title = request.form.get('title', task.title)
    task.description = request.form.get('description', task.description)
    task.status = request.form.get('status', task.status)
    task.priority = request.form.get('priority', task.priority)
    
    # Commit changes to database
    db.session.commit()
    
    return redirect(f'/task/{task_id}')
```

**Key Points:**
- `Task.query.get(id)` - Gets the task
- Modify the object's attributes
- `db.session.commit()` - Saves changes

<!-- ### DELETE - Removing Tasks (Flask Shell Example)

```python
# Get the task
>>> task = Task.query.get(3)

# Delete it
>>> db.session.delete(task)
>>> db.session.commit()

# Verify it's gone
>>> Task.query.get(3)
None
``` -->

### DELETE - Removing Tasks (Route Example)

Here's how to delete a task using a Flask route:

```python
@app.route('/task/delete/<int:task_id>')
def delete_task(task_id):
    # Get the task from database
    task = Task.query.get(task_id)
    
    if not task:
        return "Task not found", 404
    
    # Delete the task
    db.session.delete(task)
    
    # Commit to remove from database
    db.session.commit()
    
    return redirect('/tasks')
```

**Key Points:**
- `Task.query.get(id)` - Gets the task
- `db.session.delete(task)` - Marks it for deletion
- `db.session.commit()` - Removes it from database

---

## 7. Database Relationships: Connecting Tables

Relationships help us connect data between different tables. There are three main types of relationships you need to understand.

### Understanding Relationships with Real-World Examples

Think of relationships like connections in real life:
- **One-to-One**: One person has one passport
- **One-to-Many**: One teacher has many students
- **Many-to-Many**: Many students enroll in many courses

---

### 1. One-to-Many Relationship (Most Common)

**Concept:** One record in Table A can have many related records in Table B, but each record in Table B belongs to only one record in Table A.

**Real-World Example:**
- One **User** can create many **Tasks**
- But each **Task** belongs to only one **User**

**Visual Representation:**
```
User (1) ────────< (Many) Tasks
  ├─ User 1
  │   ├─ Task 1
  │   ├─ Task 2
  │   └─ Task 3
  └─ User 2
      ├─ Task 4
      └─ Task 5
```

#### TaskMaster Example:

```python
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(50), unique=True, nullable=False)
    
    # One-to-Many: One user has many tasks
    tasks = db.relationship('Task', backref='owner', lazy=True)

class Task(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), nullable=False)
    
    # Foreign Key: This task belongs to one user
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)
```

**How to Use:**
```python
# Get all tasks for a user
user = User.query.get(1)
all_user_tasks = user.tasks  # Returns list of Task objects

# Get the owner of a task
task = Task.query.get(1)
task_owner = task.owner  # Returns User object (via backref)
```

#### Placement Portal Example:

```python
class Company(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), nullable=False)
    
    # One company can create many placement drives
    drives = db.relationship('PlacementDrive', backref='company', lazy=True)

class PlacementDrive(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), nullable=False)
    
    # Foreign Key: This drive belongs to one company
    company_id = db.Column(db.Integer, db.ForeignKey('company.id'), nullable=False)
```

**How to Use:**
```python
# Get all drives for a company
company = Company.query.get(1)
all_drives = company.drives  # Returns list of PlacementDrive objects

# Get the company that created a drive
drive = PlacementDrive.query.get(1)
drive_company = drive.company  # Returns Company object
```

**Key Points:**
- The "Many" side (Task/PlacementDrive) has the Foreign Key (`user_id`/`company_id`)
- The "One" side (User/Company) has the relationship (`tasks`/`drives`)
- `backref` creates a reverse relationship automatically

---

### 2. One-to-One Relationship (Less Common)

**Concept:** One record in Table A relates to exactly one record in Table B, and vice versa.

**Real-World Example:**
- One **User** has one **Profile** (with additional details)
- One **Profile** belongs to one **User**

**Visual Representation:**
```
User (1) ──────── (1) Profile
  ├─ User 1 ──────── Profile 1
  └─ User 2 ──────── Profile 2
```

#### TaskMaster Example (Optional Enhancement):

```python
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(50), unique=True, nullable=False)
    
    # One-to-One: One user has one profile
    profile = db.relationship('UserProfile', backref='user', uselist=False)

class UserProfile(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    bio = db.Column(db.Text)
    avatar_url = db.Column(db.String(200))
    
    # Foreign Key: This profile belongs to one user
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), unique=True, nullable=False)
```

**How to Use:**
```python
# Get user's profile
user = User.query.get(1)
user_profile = user.profile  # Returns single UserProfile object (not a list!)

# Get profile's user
profile = UserProfile.query.get(1)
profile_user = profile.user  # Returns User object
```

#### Placement Portal Example:

```python
class Student(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), nullable=False)
    email = db.Column(db.String(120), unique=True)
    
    # One student has one academic record
    academic_record = db.relationship('AcademicRecord', backref='student', uselist=False)

class AcademicRecord(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    cgpa = db.Column(db.Float)
    semester = db.Column(db.Integer)
    
    # Foreign Key: This record belongs to one student
    student_id = db.Column(db.Integer, db.ForeignKey('student.id'), unique=True, nullable=False)
```

**Key Points:**
- Use `uselist=False` in the relationship (tells SQLAlchemy it's one-to-one, not a list)
- The Foreign Key has `unique=True` (ensures one-to-one constraint)

---

### 3. Many-to-Many Relationship (Advanced)

**Concept:** Records in Table A can relate to many records in Table B, and records in Table B can relate to many records in Table A.

**Real-World Example:**
- Many **Students** can apply to many **PlacementDrives**
- Many **PlacementDrives** can have many **Students** applying

**Visual Representation:**
```
Students (Many) ──────── (Many) PlacementDrives
  ├─ Student 1 ──────── Drive 1
  │   └─────────────── Drive 2
  └─ Student 2 ──────── Drive 1
      └─────────────── Drive 3
```

#### Placement Portal Example:

```python
# Association Table (Junction Table)
# This table connects Students and PlacementDrives
student_drive = db.Table('student_drive',
    db.Column('student_id', db.Integer, db.ForeignKey('student.id'), primary_key=True),
    db.Column('drive_id', db.Integer, db.ForeignKey('placement_drive.id'), primary_key=True)
)

class Student(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), nullable=False)
    
    # Many-to-Many: One student can apply to many drives
    applied_drives = db.relationship('PlacementDrive', 
                                     secondary=student_drive,
                                     backref='applicants')

class PlacementDrive(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), nullable=False)
    
    # The relationship is defined on Student, but backref creates reverse access
    # So you can also do: drive.applicants to get all students who applied
```

**How to Use:**
```python
# Student applies to a drive
student = Student.query.get(1)
drive = PlacementDrive.query.get(1)
student.applied_drives.append(drive)
db.session.commit()

# Get all drives a student applied to
student = Student.query.get(1)
all_applied_drives = student.applied_drives  # Returns list of PlacementDrive objects

# Get all students who applied to a drive
drive = PlacementDrive.query.get(1)
all_applicants = drive.applicants  # Returns list of Student objects
```

**Alternative: Using Application Model (Better for Placement Portal)**

For Placement Portal, you'll likely want to store additional information about the application (like status, date applied, etc.), so use an intermediate model:

```python
class Application(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    status = db.Column(db.String(20), default='applied')  # applied, shortlisted, selected, rejected
    applied_date = db.Column(db.DateTime, default=datetime.utcnow)
    
    # Foreign Keys
    student_id = db.Column(db.Integer, db.ForeignKey('student.id'), nullable=False)
    drive_id = db.Column(db.Integer, db.ForeignKey('placement_drive.id'), nullable=False)
    
    # Relationships
    student = db.relationship('Student', backref='applications')
    drive = db.relationship('PlacementDrive', backref='applications')

class Student(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), nullable=False)

class PlacementDrive(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), nullable=False)
```

**How to Use:**
```python
# Student applies to a drive
new_application = Application(
    student_id=1,
    drive_id=1,
    status='applied'
)
db.session.add(new_application)
db.session.commit()

# Get all applications for a student
student = Student.query.get(1)
student_applications = student.applications  # Returns list of Application objects

# Get all applications for a drive
drive = PlacementDrive.query.get(1)
drive_applications = drive.applications  # Returns list of Application objects
```

**Key Points:**
- Many-to-Many requires an intermediate/junction table
- You can use a simple `db.Table` or a full model (if you need extra fields)
- The intermediate table has Foreign Keys to both tables

---

## Relationship Summary Table

| Relationship Type | TaskMaster Example | Placement Portal Example | When to Use |
|------------------|-------------------|-------------------------|-------------|
| **One-to-Many** | User → Tasks | Company → PlacementDrives | Most common. One parent, many children |
| **One-to-One** | User → Profile | Student → AcademicRecord | When you want to separate related data |
| **Many-to-Many** | (Not used in TaskMaster) | Students ↔ PlacementDrives | When both sides can have multiple connections |

---

## Quick Reference: Relationship Syntax

### One-to-Many
```python
# Parent Model (One)
class Parent(db.Model):
    children = db.relationship('Child', backref='parent', lazy=True)

# Child Model (Many)
class Child(db.Model):
    parent_id = db.Column(db.Integer, db.ForeignKey('parent.id'), nullable=False)
```

### One-to-One
```python
# Model A
class ModelA(db.Model):
    model_b = db.relationship('ModelB', backref='model_a', uselist=False)

# Model B
class ModelB(db.Model):
    model_a_id = db.Column(db.Integer, db.ForeignKey('model_a.id'), unique=True, nullable=False)
```

### Many-to-Many (Simple)
```python
# Junction Table
association_table = db.Table('association',
    db.Column('a_id', db.Integer, db.ForeignKey('model_a.id'), primary_key=True),
    db.Column('b_id', db.Integer, db.ForeignKey('model_b.id'), primary_key=True)
)

# Model A
class ModelA(db.Model):
    model_bs = db.relationship('ModelB', secondary=association_table, backref='model_as')
```

---

## 8. Complete Module 2 App

```python
from flask import Flask, request, redirect
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
        return "<h1>No tasks yet!</h1><p>Create tasks using the create route.</p>"
    
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

# CREATE - Add new task via route
@app.route('/task/create', methods=['GET', 'POST'])
def create_task():
    if request.method == 'POST':
        title = request.form.get('title')
        description = request.form.get('description', '')
        priority = request.form.get('priority', 'medium')
        status = request.form.get('status', 'pending')
        
        new_task = Task(title=title, description=description, priority=priority, status=status)
        db.session.add(new_task)
        db.session.commit()
        
        return redirect('/tasks')
    
    return """
    <h1>Create New Task</h1>
    <form method="POST">
        <p>Title: <input type="text" name="title" required></p>
        <p>Description: <textarea name="description"></textarea></p>
        <p>Priority: 
            <select name="priority">
                <option value="low">Low</option>
                <option value="medium" selected>Medium</option>
                <option value="high">High</option>
            </select>
        </p>
        <p>Status: 
            <select name="status">
                <option value="pending" selected>Pending</option>
                <option value="in-progress">In Progress</option>
                <option value="completed">Completed</option>
            </select>
        </p>
        <button type="submit">Create Task</button>
    </form>
    <a href="/tasks">Back to Tasks</a>
    """

# UPDATE - Update existing task via route
@app.route('/task/update/<int:task_id>', methods=['POST'])
def update_task(task_id):
    task = Task.query.get(task_id)
    
    if not task:
        return "Task not found", 404
    
    task.title = request.form.get('title', task.title)
    task.description = request.form.get('description', task.description)
    task.status = request.form.get('status', task.status)
    task.priority = request.form.get('priority', task.priority)
    
    db.session.commit()
    
    return redirect(f'/task/{task_id}')

# DELETE - Delete task via route
@app.route('/task/delete/<int:task_id>')
def delete_task(task_id):
    task = Task.query.get(task_id)
    
    if not task:
        return "Task not found", 404
    
    db.session.delete(task)
    db.session.commit()
    
    return redirect('/tasks')

# Initialize sample data
@app.route('/init-data')
def init_data():
    Task.query.delete()
    db.session.commit()
    
    tasks = [
        Task(title='Complete Flask Tutorial', description='Finish Module 1 and Module 2', priority='high', status='in-progress'),
        Task(title='Setup Database', description='Configure SQLAlchemy with SQLite', priority='high', status='completed'),
        Task(title='Build Dashboard UI', description='Create task list with Bootstrap', priority='medium'),
    ]
    
    db.session.add_all(tasks)
    db.session.commit()
    
    return f"Added {Task.query.count()} tasks! <a href='/tasks'>View Tasks</a>"


# ==================== MAIN ====================

if __name__ == '__main__':
    with app.app_context():
        db.create_all()
    app.run(debug=True)
```

---

## 9. Populating with Sample Data

You can create a route to add sample data programmatically:

```python
@app.route('/init-data')
def init_data():
    # Clear existing data (optional)
    Task.query.delete()
    db.session.commit()
    
    # Add sample tasks
    tasks = [
        Task(title='Complete Flask Tutorial', description='Finish Module 1 and Module 2', priority='high', status='in-progress'),
        Task(title='Setup Database', description='Configure SQLAlchemy with SQLite', priority='high', status='completed'),
        Task(title='Build Dashboard UI', description='Create task list with Bootstrap', priority='medium'),
        Task(title='Add User Authentication', description='Login and signup forms', priority='medium'),
        Task(title='Write Documentation', description='Document all routes and models', priority='low'),
        Task(title='Deploy to Server', description='Host on PythonAnywhere', priority='low'),
    ]
    
    db.session.add_all(tasks)
    db.session.commit()
    
    return f"Added {Task.query.count()} tasks! <a href='/tasks'>View Tasks</a>"
```

Visit `/init-data` once to populate your database, then visit `/tasks` to see your data.

<!-- ## 8. Populating with Sample Data (Flask Shell - Commented Out)

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
    Task(title='Complete Flask Tutorial', description='Finish Module 1 and Module 2', priority='high', status='in-progress'),
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

Now run the app and visit `/tasks` to see your data. -->

---

## 10. Module 2 Exercises

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

## 11. How This Relates to Your Final Project

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

## 12. Key Takeaways

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

## 13. Quick Reference

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

[Previous: Module 1](module1-flask-routing.html){: .btn } [Next: Module 3](module3-jinja-bootstrap.html){: .btn .btn-primary }
