---
title: Home
layout: home
nav_order: 1
---

# MAD 1 Bootcamp: Task Management App

An 8-day roadmap to learn all core concepts required for building a **Placement Portal Application** using a simple **Task Management App** example.

---

## Core Stack

| Technology | Purpose |
|------------|---------|
| **Flask** | Backend logic and routing |
| **SQLite and SQLAlchemy** | Database and ORM |
| **Jinja2 and Bootstrap 5** | Dynamic Frontend and Styling |

---

## 8-Day Roadmap

| Day | Topic | Description |
|-----|-------|-------------|
| **Day 1** | [The Skeleton](day1-flask-routing.html) | Flask setup, routing and view functions |
| **Day 2** | [The Foundation](day2-database.html) | SQLite, SQLAlchemy and CRUD operations |
| **Day 3** | [The Face](day3-jinja-bootstrap.html) | Jinja2 templates and Bootstrap UI |
| **Day 4** | [The Gatekeeper](day4-auth.html) | User authentication and roles |
| **Day 5** | [The Interaction](day5-interaction.html) | Forms and database relationships |
| **Day 6** | [The Approval Flow](day6-approval-flow.html) | State management and flash messages |
| **Day 7** | [The Bridge](day7-search-api.html) | Search functionality and API basics |
| **Day 8** | [The Polish](day8-polish.html) | Refactoring and error handling |

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

[Start Day 1](day1-flask-routing.html){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }
