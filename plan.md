# MAD 1 Bootcamp: 9-Module Roadmap (Task Management App Example)

This plan is designed to teach you all the core concepts required for your **Placement Portal Application** using a simple **Task Management App**. Each day builds on the previous one, mapping directly to features you'll need for your final project.

## Core Stack
- **Flask**: Backend logic
- **SQLite & SQLAlchemy**: Database & ORM
- **Jinja2 & Bootstrap 5**: Dynamic Frontend & Styling

---

## Module 0: Introduction (The Stack)
**Goal:** Understand the "Why" behind our choice of tools.

- **Concepts:**
    - HTML5, Jinja2, Flask, and SQLite overview.
    - Flask as a micro-framework (Simplicity vs. Power).
    - Mapping TaskMaster features to the Placement Portal project.
- **Task Management Activity:** 
    - None (Overview only).
- **Relates to Final Project:** Understanding the ecosystem you'll use for the entire term.

## Module 1: The Skeleton (Flask & Routing)
**Goal:** Setup the environment and build the basic structure of a web app.

- **Concepts:**
    - Virtual Environments (`venv`).
    - Flask App instance and `app.run()`.
    - Routes (`@app.route('/')`) and View Functions.
    - Dynamic Routing (e.g., `/user/<username>`).
- **Task Management Activity:** 
    - Create a "Welcome to TaskMaster" homepage.
    - Create a route `/category/<name>` to display a "filtered" category page.
- **Relates to Final Project:** Establishing the base layout and dynamic URLs for Admin/Company/Student dashboards.

## Module 2: The Foundation (SQLite & SQLAlchemy)
**Goal:** Make data persist using a database.

- **Concepts:**
    - Intro to SQL vs. ORM (Object Relational Mapping).
    - Defining Models (`db.Model`).
    - `db.create_all()` and database initialization.
    - CRUD Operations in Flask Shell (`db.session.add`, `query`, `delete`).
- **Task Management Activity:** 
    - Define a `Task` model (ID, Title, Description, Status, Priority).
    - Populate the database with dummy tasks using the Python terminal.
- **Relates to Final Project:** Creating tables for `Student`, `Company`, `PlacementDrive`, and `Application`.

## Module 3: The Face (Jinja2 & Bootstrap)
**Goal:** Build a professional UI that displays database data.

- **Concepts:**
    - Template Inheritance (`base.html`, `{% block %}`).
    - Jinja2 Logic: `{% for %}`, `{% if %}`, `{{ variable }}`.
    - Bootstrap 5 Integration: Navbar, Cards, Tables, Buttons.
- **Task Management Activity:** 
    - Create a "Dashboard" that lists all Tasks in a Bootstrap Card grid or Table.
    - Use color-coding for status (e.g., Red for "Pending", Green for "Done").
- **Relates to Final Project:** Building the distinct dashboards for different user roles.

## Module 4: The Gatekeeper (User Auth & Role Setup)
**Goal:** Secure the app and distinguish between different users.

- **Concepts:**
    - User Model with `password_hash` (using `werkzeug.security`).
    - Login/Signup logic with `flask.session`.
    - Role-based branching (Intro to checking `user.role`).
- **Task Management Activity:** 
    - Create a User model with two roles: `Manager` and `TeamMember`.
    - Restrict the "Create Task" button to `Manager` only.
- **Relates to Final Project:** Implementing Admin, Company, and Student authentication and restricted access.

## Module 5: The Interaction (Forms & Relationships)
**Goal:** Handle user input and link data together (Foreign Keys).

- **Concepts:**
    - Handling `POST` requests from HTML forms.
    - One-to-Many Relationships (`db.ForeignKey`, `db.relationship`).
    - Form Validation (Basic backend checks).
- **Task Management Activity:** 
    - Link `Tasks` to `Users` (Who created/is assigned the task?).
    - Create a "Create Task" form and an "Edit Task" page.
- **Relates to Final Project:** Linking `Drives` to `Companies` and `Applications` to `Students` & `Drives`.

## Module 6: The Approval Flow (State Management & Flash)
**Goal:** Implement business logic and user feedback.

- **Concepts:**
    - Updating object status in the database.
    - `flash()` messages for user notifications.
    - Difference between SSR and simple logic updates.
- **Task Management Activity:** 
    - Add a "Mark as Complete" button.
    - Implement a "Review" stage where a Manager must approve a Task before it's "Finished".
- **Relates to Final Project:** Admin approving Company/Drive, Company shortlisting Students.

## Module 7: The Bridge (Search & API Basics)
**Goal:** Allow users to find data and expose endpoints.

- **Concepts:**
    - Querying with filters: `Task.query.filter_by(...)` or `filter(Task.title.contains(...))`.
    - Handling search inputs from forms (SSR).
    - `jsonify` from Flask for simple API endpoints.
- **Task Management Activity:** 
    - Create a search bar to filter tasks by Title or Priority.
    - Build `/api/tasks` to return all tasks in JSON format.
- **Relates to Final Project:** Admin searching for Students/Companies by name/ID; Optional API requirements.

## Module 8: The Polish (Refactoring & Error Handling)
**Goal:** Clean up the code, add validation, and prepare for submission.

- **Concepts:**
    - Backend Validation: Ensuring no empty fields or duplicates before committing.
    - Custom Error Pages (404, 500).
    - Modularity: Moving models to `models.py` and logic to `routes.py`.
- **Task Management Activity:** 
    - Prevent adding a Task with an empty title (with a flash message).
    - Build a custom 404 page.
    - Organize the project into the required folder structure.
- **Relates to Final Project:** Finalizing the submission zip and ensuring clean, validated code.

---

### Comparison: Example vs. Final Project
| Concept | Task Management Example | Placement Portal Project |
| :--- | :--- | :--- |
| **User Role 1** | Manager | Admin (Institute) |
| **User Role 2** | Team Member | Company / Student |
| **Main Entity** | Task | Placement Drive |
| **Workflow** | Task Creation -> Review -> Complete | Drive Creation -> Approval -> Application |
| **Interaction** | Assign Task to User | Student Applying to Drive |
| **Search** | Filter tasks by name | Search students/companies |
| **Restricted** | Buttons visible only to Role | Role-based Dashboard Access |