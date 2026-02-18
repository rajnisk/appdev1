---
title: "Day 1: The Skeleton (Flask & Routing)"
layout: default
nav_order: 2
---

# Day 1: The Skeleton (Flask & Routing)

**Goal:** Setup the environment and build the basic structure of a web app.

---

## Learning Objectives

By the end of this day, you will be able to:

- Set up a Python virtual environment
- Create a basic Flask application
- Understand routes and view functions
- Implement dynamic routing with URL parameters

---

## 1. Virtual Environments

A virtual environment is an isolated Python environment that keeps your project dependencies separate from other projects.

### Why Use Virtual Environments?

- **Isolation:** Each project has its own dependencies
- **Version Control:** Different projects can use different package versions
- **Clean Setup:** Easy to recreate the environment on another machine

### Creating a Virtual Environment

```bash
# Navigate to your project folder
cd taskmaster

# Create a virtual environment named 'venv'
python -m venv venv
```

### Activating the Virtual Environment

```bash
# On Linux/macOS
source venv/bin/activate

# On Windows
venv\Scripts\activate
```

{: .note }
When activated, you'll see `(venv)` at the beginning of your terminal prompt.

### Deactivating

```bash
deactivate
```

---

## 2. Installing Flask

With your virtual environment activated:

```bash
pip install flask
```

Verify installation:

```bash
python -c "import flask; print(flask.__version__)"
```

---

## 3. Your First Flask App

Create a new file called `app.py`:

```python
from flask import Flask

# Create the Flask application instance
app = Flask(__name__)

# Define a route for the homepage
@app.route('/')
def home():
    return "Welcome to TaskMaster!"

# Run the app
if __name__ == '__main__':
    app.run(debug=True)
```

### Understanding the Code

| Code | Explanation |
|------|-------------|
| `Flask(__name__)` | Creates the app instance. `__name__` helps Flask find resources. |
| `@app.route('/')` | A **decorator** that maps a URL to a function |
| `def home()` | A **view function** that returns what the user sees |
| `debug=True` | Enables auto-reload and detailed error messages |

### Running the App

```bash
python app.py
```

Open your browser and go to: `http://127.0.0.1:5000/`

{: .highlight }
You should see "Welcome to TaskMaster!" displayed in your browser.

---

## 4. Adding More Routes

Let's add more pages to our app:

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "<h1>Welcome to TaskMaster!</h1><p>Your personal task management app.</p>"

@app.route('/about')
def about():
    return "<h1>About TaskMaster</h1><p>A simple app to manage your daily tasks.</p>"

@app.route('/tasks')
def tasks():
    return "<h1>Your Tasks</h1><p>No tasks yet. Start adding some!</p>"

if __name__ == '__main__':
    app.run(debug=True)
```

Now you can visit:
- `http://127.0.0.1:5000/` - Homepage
- `http://127.0.0.1:5000/about` - About page
- `http://127.0.0.1:5000/tasks` - Tasks page

---

## 5. Dynamic Routing

Dynamic routes allow you to capture values from the URL.

### Basic Dynamic Route

```python
@app.route('/category/<name>')
def category(name):
    return f"<h1>Category: {name}</h1><p>Showing tasks in the '{name}' category.</p>"
```

Now visiting `/category/work` shows: **Category: work**

### Multiple URL Parameters

```python
@app.route('/user/<username>/task/<int:task_id>')
def user_task(username, task_id):
    return f"<h1>{username}'s Task #{task_id}</h1>"
```

### URL Converters

| Converter | Description | Example |
|-----------|-------------|---------|
| `string` | (default) Any text without slashes | `/user/<name>` |
| `int` | Accepts positive integers | `/task/<int:id>` |
| `float` | Accepts positive floating point | `/price/<float:value>` |
| `path` | Like string but includes slashes | `/file/<path:filepath>` |

---

## 6. Complete Day 1 App

Here's our complete TaskMaster skeleton:

```python
from flask import Flask

app = Flask(__name__)

# Homepage
@app.route('/')
def home():
    return """
    <html>
    <head><title>TaskMaster</title></head>
    <body>
        <h1>Welcome to TaskMaster!</h1>
        <p>Your personal task management application.</p>
        <nav>
            <a href="/tasks">View Tasks</a> |
            <a href="/category/work">Work Tasks</a> |
            <a href="/category/personal">Personal Tasks</a> |
            <a href="/about">About</a>
        </nav>
    </body>
    </html>
    """

# Tasks page
@app.route('/tasks')
def tasks():
    return """
    <h1>All Tasks</h1>
    <p>Your tasks will appear here once we connect a database!</p>
    <a href="/">Back to Home</a>
    """

# Category filter page (Dynamic Route)
@app.route('/category/<name>')
def category(name):
    return f"""
    <h1>Category: {name.title()}</h1>
    <p>Showing all tasks in the <strong>{name}</strong> category.</p>
    <a href="/">Back to Home</a>
    """

# Task detail page (Dynamic Route with int converter)
@app.route('/task/<int:task_id>')
def task_detail(task_id):
    return f"""
    <h1>Task #{task_id}</h1>
    <p>Details for task {task_id} will appear here.</p>
    <a href="/tasks">Back to Tasks</a>
    """

# About page
@app.route('/about')
def about():
    return """
    <h1>About TaskMaster</h1>
    <p>TaskMaster is a simple task management app built with Flask.</p>
    <p>Created as part of the MAD 1 Bootcamp.</p>
    <a href="/">Back to Home</a>
    """

if __name__ == '__main__':
    app.run(debug=True)
```

---

## Day 1 Exercises

1. **Add a Priority Route:** Create `/priority/<level>` that displays tasks by priority (high, medium, low)

2. **User Profile:** Create `/user/<username>` that shows a welcome message with the username

3. **Status Filter:** Create `/status/<status>` for filtering by status (pending, in-progress, completed)

{: .important }
**Challenge:** Can you add a route that takes both a category AND a status? Example: `/category/work/status/pending`

---

## How This Relates to Your Final Project

| TaskMaster | Placement Portal |
|------------|------------------|
| Homepage route `/` | Landing page for all users |
| `/category/<name>` | `/dashboard/admin`, `/dashboard/company`, `/dashboard/student` |
| `/task/<int:id>` | `/drive/<int:drive_id>`, `/student/<int:student_id>` |
| Dynamic routing | Role-based dynamic URLs |

---

## Key Takeaways

1. **Flask App Instance:** `app = Flask(__name__)` creates your application
2. **Routes:** Use `@app.route('/path')` to map URLs to functions
3. **View Functions:** Return what the user sees (HTML, text, etc.)
4. **Dynamic Routes:** Use `<variable>` to capture URL values
5. **Debug Mode:** Always use `debug=True` during development

---

[Next: Day 2 - The Foundation](day2-database.html){: .btn .btn-primary }
