---
title: "Quick Notes & Cheat Sheet"
layout: default
nav_order: 11
---

# Quick Notes & Cheat Sheet

Quick revision guide for viva and exams. For detailed explanations, refer to the respective modules.

---

## 1. Flask Basics

**What is Flask?**
- Lightweight Python web framework
- Maps URLs to Python functions (routes)
- Simple and beginner-friendly

**Key Terms:**
- **Route**: URL path (e.g., `/tasks`)
- **View Function**: Python function that handles the route
- **Dynamic Route**: URL with variables (e.g., `/task/<id>`)
- **GET**: View page, **POST**: Submit form

[📖 Module 1: Flask & Routing](module1-flask-routing.html)

---

## 2. Database & SQLAlchemy

**Why Database?**
- Save data permanently 
- Organize data in tables
- Find data quickly

**SQL vs ORM:**
- **SQL**: Write database queries directly
- **ORM**: Write Python code, SQLAlchemy converts to SQL
- **Easier**: Work with Python objects instead of SQL

**Model = Table:**
- Model is a Python class
- Each column is a field (id, title, status)
- Primary Key = unique ID for each row

**CRUD Operations:**
- **Create**: `db.session.add()` + `commit()`
- **Read**: `Task.query.all()`, `.get(id)`, `.filter_by()`
- **Update**: Change object + `commit()`
- **Delete**: `db.session.delete()` + `commit()`

**Remember:** Always `commit()` after changes!

[📖 Module 2: Database & SQLAlchemy](module2-database.html)

---

## 3. Relationships

**One-to-Many** (Most Common)
- One parent has many children
- Example: One User → Many Tasks
- Parent has `relationship()`, Child has `ForeignKey()`
- Placement Portal: Company → PlacementDrives

**Many-to-Many**
- Many records connect to many other records
- Example: Many Students apply to Many Drives
- Use Application model to store the connection + extra info (status, date)

[📖 Module 2: Relationships](module2-database.html#7-database-relationships-connecting-tables)

---

## 4. Jinja2 Templates

**What is it?**
- Template engine for Python
- Write HTML with placeholders for data
- Flask fills in the data

**Basic Syntax:**
- `{{ variable }}` - Show value
- `{% for item in list %}` - Loop
- `{% if condition %}` - If/else
- `{% extends "base.html" %}` - Use parent template
- `{% block %}` - Replaceable section

**Template Inheritance:**
- `base.html` = parent (common layout)
- Other templates extend it
- Saves time, keeps design consistent

[📖 Module 3: Jinja2 & Bootstrap](module3-jinja-bootstrap.html)

---

## 5. Forms & CRUD

**Form Handling:**
- GET = show form
- POST = process form data
- Use `request.form.get('field_name')` to get data

**CRUD via Forms:**
- **Create**: Form → Save to database
- **Read**: Query database → Show in template
- **Update**: Load existing data → Edit → Save
- **Delete**: Click delete → Remove from database

[📖 Module 5: Forms & CRUD](module5-interaction.html)

---

## 6. Authentication & Sessions

**Password Security:**
- Never store passwords as plain text!
- Use hashing (convert to code)
- `generate_password_hash()` - when signing up
- `check_password_hash()` - when logging in

**Sessions:**
- Remember user after login
- Store: `session['user_id'] = user.id`
- Check: `if 'user_id' in session:`
- Clear: `session.clear()` (logout)

**Protect Routes:**
- Check if user is logged in
- Redirect to login if not logged in
- Show only user's own data

[📖 Module 4: Authentication](module4-auth.html)

---

## 7. Flash Messages

**What are they?**
- One-time messages to user
- Success, error, warning, info
- Show after actions (login, create, delete)

**How it works:**
- Python: `flash('Message', 'success')`
- Template: Display using `get_flashed_messages()`

[📖 Module 6: Flash Messages](module6-approval-flow.html#2-flash-messages)

---

## 8. Search

**LIKE Pattern Matching:**
- `LIKE '%query%'` - Find anywhere
- `LIKE 'query%'` - Starts with
- `LIKE '%query'` - Ends with
- `%` = any characters

**Example:**
```python
results = Task.query.filter(Task.title.like(f'%{query}%')).all()
```

[📖 Module 7: Search](module7-search-api.html#1-search-functionality)

---

## 9. API Endpoints

**What is API?**
- Returns data (JSON) instead of HTML
- For apps/machines to use, not humans
- Use `jsonify()` to return JSON

**Difference:**
- `render_template()` → HTML (for humans)
- `jsonify()` → JSON (for machines/apps)

[📖 Module 7: API](module7-search-api.html#2-returning-json-the-api)

---

## 10. Graphs & Charts

**How to create:**
1. Use `matplotlib` library
2. Get data from database
3. Create plot (bar chart, etc.)
4. Convert to base64 image
5. Display in template

**Key:** Always use `matplotlib.use('Agg')` and `plt.close()`

[📖 Module 7: Graphs](module7-search-api.html#3-creating-graphs-and-charts)

---

## 11. Error Handling

**Custom Error Pages:**
- 404 = Page not found
- 500 = Server error
- Use `@app.errorhandler(404)` to catch errors

**Validation:**
- Always check user input
- Check for empty fields
- Check for duplicates
- Check ownership before edit/delete

[📖 Module 8: Error Handling](module8-polish.html)

---

## 12. Important Rules

**Security:**
- Hash passwords (never plain text)
- Protect routes (check session)
- Validate all input
- Filter by user_id (show only own data)

**Best Practices:**
- Always `commit()` after database changes
- Use flash messages for feedback
- Organize code (models.py, app.py)
- Keep Bootstrap minimal

---

---

## Web Application Architecture

### Client-Server Model
- **Client**: Browser (requests pages)
- **Server**: Flask application (processes requests, sends responses)
- **Request-Response Cycle**: Client sends request → Server processes → Server sends response

### HTTP Protocol
- **HTTP (HyperText Transfer Protocol)**: Rules for communication between client and server
- **Stateless**: Each request is independent (server doesn't remember previous requests)
- **Methods**: GET (retrieve), POST (submit), PUT (update), DELETE (remove)
- **Status Codes**: 200 (OK), 404 (Not Found), 500 (Server Error)

### Static vs Dynamic Websites
- **Static**: Same HTML for everyone, no database
- **Dynamic**: Content changes based on user/data, uses database and templates

---

## Flask Framework Theory

### What is a Web Framework?
- **Framework**: Pre-built tools and structure for building web apps
- **Flask**: Micro-framework (minimal, flexible)
- **WSGI**: Web Server Gateway Interface (how Python web apps communicate with servers)

### Why Flask is "Micro"?
- **Minimal core**: Only essential features included
- **Extensible**: Add features as needed (database, auth, etc.)
- **No enforced structure**: You decide how to organize code
- **Lightweight**: Small codebase, fast startup


### Flask Application Lifecycle
1. Client sends HTTP request
2. Flask receives request
3. Matches URL to route
4. Executes view function
5. Returns response (HTML/JSON)
6. Client receives and displays

---

## Database Theory

### What is a Database?
- **Database**: Organized collection of data
- **DBMS**: Database Management System (software that manages database)
- **SQLite**: File-based database (no separate server needed)

### Database Concepts
- **Table**: Collection of rows and columns (like Excel sheet)
- **Row (Record)**: One entry in table
- **Column (Field)**: One attribute of data
- **Primary Key**: Unique identifier for each row
- **Foreign Key**: Reference to another table's primary key

### ACID Properties
- **Atomicity**: Transaction completes fully or not at all
- **Consistency**: Data remains valid after transaction
- **Isolation**: Concurrent transactions don't interfere
- **Durability**: Committed data persists even after system failure

### Normalization (Basic)
- **Purpose**: Reduce data redundancy, improve data integrity
- **1NF**: Each column contains single value
- **2NF**: No partial dependencies
- **3NF**: No transitive dependencies

### SQLite vs Other Databases
- **SQLite**: File-based, no server, good for small-medium apps
- **MySQL/PostgreSQL**: Server-based, better for large applications
- **When to use SQLite**: Learning, small projects, prototypes

---

## Template Engine Theory

### Why Template Engines?
- **Separation of Concerns**: Keep Python logic separate from HTML
- **Reusability**: Write HTML once, use with different data
- **Security**: Prevents code injection
- **Maintainability**: Easier to update design

### Server-Side Rendering (SSR)
- **SSR**: Server generates HTML, sends to client
- **Advantage**: SEO friendly, fast initial load
- **Flask + Jinja2**: Classic SSR approach
- **Alternative**: Client-side rendering (React, Vue)

### Template Inheritance Benefits
- **DRY Principle**: Don't Repeat Yourself
- **Consistency**: Same layout across pages
- **Maintainability**: Update base template, all pages update
- **Modularity**: Break page into reusable blocks

---

## MVC Pattern

### What is MVC?
- **Model**: Data structure (database models)
- **View**: Presentation (HTML templates)
- **Controller**: Logic (Flask routes)

### How Flask Uses MVC
- **Model**: `models.py` (User, Task classes)
- **View**: `templates/` (HTML files)
- **Controller**: `app.py` (routes and logic)

### Benefits
- **Separation**: Each part has clear responsibility
- **Maintainability**: Easy to find and fix issues
- **Scalability**: Easy to add features
- **Testing**: Can test each part independently

---

## HTTP Methods & Status Codes

### HTTP Methods
- **GET**: Retrieve data (view page, search)
- **POST**: Submit data (form submission, create)
- **PUT**: Update existing resource
- **DELETE**: Remove resource
- **PATCH**: Partial update

### Status Codes
- **2xx Success**: 200 (OK), 201 (Created)
- **3xx Redirection**: 301 (Moved), 302 (Found)
- **4xx Client Error**: 400 (Bad Request), 401 (Unauthorized), 404 (Not Found)
- **5xx Server Error**: 500 (Internal Server Error), 503 (Service Unavailable)

---

## Web Security Basics

### Authentication vs Authorization
- **Authentication**: Verifying who you are (login)
- **Authorization**: What you're allowed to do (permissions)

### Common Security Practices
- **Password Hashing**: Never store plain text
- **HTTPS**: Encrypt data in transit
- **Input Validation**: Check all user input
- **SQL Injection Prevention**: Use ORM (SQLAlchemy) instead of raw SQL
- **XSS Prevention**: Template engine escapes HTML automatically
- **CSRF Protection**: Verify request comes from your site

### Session Security
- **Session Hijacking**: Attacker steals session ID
- **Prevention**: Use HTTPS, secure cookies, session timeout
- **Session Storage**: Server-side (Flask sessions)

---

## API & REST Concepts

### What is REST?
- **REST**: Representational State Transfer
- **RESTful API**: API following REST principles
- **Resources**: Data entities (tasks, users)
- **HTTP Methods**: GET (read), POST (create), PUT (update), DELETE (remove)

### API Design Principles
- **Stateless**: Each request contains all needed info
- **Uniform Interface**: Consistent URL structure
- **Resource-Based**: URLs represent resources (`/api/tasks`, `/api/users`)

### JSON Format
- **JSON**: JavaScript Object Notation
- **Lightweight**: Easy to parse
- **Language Independent**: Works with any programming language
- **Structure**: Key-value pairs, arrays, nested objects

---

## Frontend vs Backend

### Backend (Server-Side)
- **Flask**: Handles business logic, database operations
- **Runs on server**: User doesn't see the code
- **Responsibilities**: Authentication, data processing, API endpoints

### Frontend (Client-Side)
- **HTML/CSS/JavaScript**: What user sees and interacts with
- **Runs in browser**: User's computer
- **Responsibilities**: Display, user interaction, styling

### How They Work Together
- Frontend sends request → Backend processes → Backend sends data → Frontend displays
- **Templates**: Backend generates HTML, sends to frontend
- **API**: Backend sends JSON, frontend JavaScript processes it

---

## Database Relationships Theory

### Why Relationships?
- **Data Integrity**: Ensure data consistency
- **Avoid Redundancy**: Don't repeat data
- **Efficient Storage**: Store data once, reference it
- **Query Flexibility**: Join tables to get related data

### Relationship Types
- **One-to-One**: Rare, used when data should be separated
- **One-to-Many**: Most common (parent-child relationship)
- **Many-to-Many**: Requires intermediate table/model

### Foreign Key Constraints
- **Referential Integrity**: Ensures foreign key points to valid record
- **Cascade Delete**: Delete parent, automatically delete children
- **Prevents Orphans**: Can't have child without parent

---

## Viva Questions

### Flask Framework
**Q: Why Flask is called micro-framework?**
- Minimal core, only essential features
- You add what you need (database, auth, etc.)
- No enforced project structure
- Lightweight and flexible

**Q: What is WSGI?**
- Web Server Gateway Interface
- Standard interface between web servers and Python web apps
- Allows Flask to work with different web servers

**Q: Flask vs Django?**
- Flask: Minimal, flexible, beginner-friendly
- Django: Full-featured, more built-in, better for large projects
- Flask gives more control, Django provides more structure

### Database Theory
**Q: What is ORM and why use it?**
- Object Relational Mapping
- Work with Python objects instead of SQL
- Type-safe, easier to maintain, database-agnostic
- SQLAlchemy converts Python to SQL automatically

**Q: What is ACID in databases?**
- Atomicity: All or nothing
- Consistency: Data stays valid
- Isolation: Transactions don't interfere
- Durability: Changes persist

**Q: Why use SQLite?**
- File-based, no separate server
- Zero configuration
- Perfect for learning and small projects
- Easy to backup (just copy file)

**Q: What is normalization?**
- Process of organizing database to reduce redundancy
- Improves data integrity
- Multiple normal forms (1NF, 2NF, 3NF)

### Template Engines
**Q: Why use template engine instead of string concatenation?**
- Separation of concerns (logic vs presentation)
- Security (prevents code injection)
- Reusability and maintainability
- Cleaner, more readable code

**Q: What is Server-Side Rendering?**
- Server generates HTML from templates
- Sends complete HTML to browser
- Good for SEO, fast initial load
- Flask + Jinja2 uses SSR

### Web Architecture
**Q: Explain Client-Server model**
- Client (browser) requests resources
- Server (Flask app) processes and responds
- HTTP protocol for communication
- Stateless: each request is independent

**Q: What is MVC pattern?**
- Model: Data (database models)
- View: Presentation (templates)
- Controller: Logic (routes)
- Separates concerns for better organization

**Q: What is REST API?**
- Representational State Transfer
- API design principles
- Uses HTTP methods (GET, POST, PUT, DELETE)
- Stateless, resource-based URLs

### Security
**Q: Why hash passwords?**
- Security: Can't reverse hash to get original password
- Even if database is compromised, passwords are safe
- One-way function: hash → password (impossible), password → hash (possible)


### Sessions & State
**Q: Why HTTP is stateless?**
- Each request is independent
- Server doesn't remember previous requests
- Allows servers to handle many clients efficiently

**Q: How do sessions work?**
- Server creates session ID, stores in cookie
- Server stores session data (user_id, etc.)
- Each request includes session ID
- Server retrieves session data using ID

**Q: What is the difference between session and cookie?**
- **Cookie**: Stored on client (browser), sent with every request
- **Session**: Stored on server, identified by session ID in cookie
- **Security**: Session data is server-side, more secure

### Database Relationships
**Q: What is Foreign Key?**
- Column that references another table's primary key
- Creates relationship between tables
- Ensures referential integrity
- Example: `user_id` in Task references `id` in User

**Q: When to use One-to-Many vs Many-to-Many?**
- **One-to-Many**: When one parent has many children (User → Tasks)
- **Many-to-Many**: When both sides can have multiple connections (Students ↔ Drives)
- Use intermediate model for Many-to-Many to store extra data

---

## Important Concepts Summary

### Web Development Basics
- Client-Server architecture
- HTTP protocol and methods
- Request-Response cycle
- Static vs Dynamic websites

### Flask Framework
- Micro-framework concept
- WSGI interface
- Routing and view functions
- Request handling

### Database
- ORM vs SQL
- ACID properties
- Normalization basics
- Relationships (One-to-Many, Many-to-Many)

### Templates
- Server-Side Rendering
- Template inheritance
- Separation of concerns
- Security (XSS prevention)

### Security
- Password hashing
- Input validation
- SQL Injection prevention
- Session management

### Architecture
- MVC pattern
- REST API principles
- Frontend vs Backend
- Code organization

---

## Quick Checklist

- [ ] Flask routes and dynamic URLs
- [ ] Database models and CRUD
- [ ] One-to-Many relationship
- [ ] Jinja2 syntax ({{ }}, {% %})
- [ ] Form handling (GET/POST)
- [ ] Password hashing
- [ ] Session management
- [ ] Route protection
- [ ] Flash messages
- [ ] Search with LIKE
- [ ] API with jsonify
- [ ] Error handling

---

## Module Links

- [Module 0: Introduction](module0-introduction.html)
- [Module 1: Flask & Routing](module1-flask-routing.html)
- [Module 2: Database](module2-database.html)
- [Module 3: Templates](module3-jinja-bootstrap.html)
- [Module 4: Authentication](module4-auth.html)
- [Module 5: Forms](module5-interaction.html)
- [Module 6: State Management](module6-approval-flow.html)
- [Module 7: Search & API](module7-search-api.html)
- [Module 8: Polish](module8-polish.html)
- [Final Code](final-taskmaster.html)

---

[Back to Home](index.html){: .btn } [View All Modules](index.html#9-module-roadmap){: .btn .btn-primary }
