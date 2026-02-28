---
title: Home
layout: home
nav_order: 1
---

# MAD 1 Bootcamp: Task Management App

A 9-module roadmap to learn all core concepts required for building a **Placement Portal Application** using a simple **Task Management App** example.

---

## Core Stack

| Technology | Purpose |
|------------|---------|
| **Flask** | Backend logic and routing |
| **SQLite and SQLAlchemy** | Database and ORM |
| **Jinja2 and Bootstrap 5** | Dynamic Frontend and Styling |

---

## 9-Module Roadmap

| Day | Topic | Description |
|-----|-------|-------------|
| **Module 0** | [Introduction](module0-introduction.html) | Why Flask, Jinja, and SQLite? |
| **Module 1** | [The Skeleton](module1-flask-routing.html) | Flask setup, routing and view functions |
| **Module 2** | [The Foundation](module2-database.html) | SQLite, SQLAlchemy and CRUD operations |
| **Module 3** | [The Face](module3-jinja-bootstrap.html) | Jinja2 templates and Bootstrap UI |
| **Module 4** | [The Gatekeeper](module4-auth.html) | User authentication and roles |
| **Module 5** | [The Interaction](module5-interaction.html) | Forms and database relationships |
| **Module 6** | [The Approval Flow](module6-approval-flow.html) | State management and flash messages |
| **Module 7** | [The Bridge](module7-search-api.html) | Search functionality and API basics |
| **Module 8** | [The Polish](module8-polish.html) | Refactoring and error handling |
| **Final** | [Full App Code](final-taskmaster.html) | Complete TaskMaster code structure |

---

## What You'll Build

A **Task Management App** with features that map directly to your final project:

| TaskMaster Feature | Placement Portal Equivalent |
|-------------------|----------------------------|
| Manager / Team Member roles | Admin / Company / Student roles |
| Task creation and assignment | Drive creation and applications |
| Task approval workflow | Drive approval by admin |
| Filter tasks by status | Filter students/companies |
| Search by title | Search by name/ID |

---

## Prerequisites

Before starting, make sure you have:

- Python 3.8 or higher installed
- A code editor (VS Code recommended)
- Basic understanding of Python
- Command line familiarity

---

## Quick Start

```bash
# Create project folder
mkdir taskmaster
cd taskmaster

# Create virtual environment
python -m venv venv

# Activate it (Linux/macOS)
source venv/bin/activate

# Activate it (Windows)
venv\Scripts\activate

# Install Flask
pip install flask flask-sqlalchemy
```

---

{: .note }
Each day builds on the previous one. Complete them in order for the best learning experience.

[Start Module 0](module0-introduction.html){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }
