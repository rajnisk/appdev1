---
title: Module 0 - Introduction
layout: default
nav_order: 2
---

# Module 0: Why This Stack?

Before we dive into the code, let's understand why we are using these specific tools and how they fit together to build a modern web application.

---

## The Core Stack

We have chosen a "Minimalist but Powerful" stack that is perfect for learning the fundamentals of web development.

| Technology | Role | Why We Use It |
| :--- | :--- | :--- |
| **HTML5** | Structure | The bedrock of every webpage. It defines *what* is on the page. |
| **Flask** | Backend | A "micro-framework" that provides just enough tools to build a web app without overwhelming you with complexity. |
| **Jinja2** | Templating | Allows us to inject dynamic data from Python into our HTML. |
| **SQLite** | Database | A lightweight database that requires zero configuration. It's just a file! |
| **SQLAlchemy** | ORM | Lets us interact with the database using Python objects instead of raw SQL queries. |

---

## Why Flask?

Flask is often called a **Micro-framework** because it doesn't force a particular project structure or require complicated boilerplate.

1.  **Readability**: Flask code looks like standard Python code. It's easy to read and understand.
2.  **Beginner Friendly**: You can build a working web server in just 5 lines of code.
3.  **Flexibility**: You only add the features you need. Unlike larger frameworks (like Django), Flask stays out of your way.
4.  **Industry Standard**: Companies like Netflix, Reddit, and LinkedIn use Flask for their services.

---

## Learning Path

In the next 8 modules, we will build a **Task Management App**. This isn't just a random project—every feature you build here maps directly to the requirements of your final **Placement Portal Application**.

1.  **Modules 1-3**: UI & Routing (The Skeleton & Face)
2.  **Modules 4-5**: Logic & Data (The Foundation & Interaction)
3.  **Modules 6-8**: Polish & Workflow (The Approval Flow & Bridge)

---

{: .highlight }
> Ready to start? Let's build the skeleton of our app in Module 1.

[Start Module 1](module1-flask-routing.html){: .btn .btn-primary }
