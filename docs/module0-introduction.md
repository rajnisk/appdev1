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

## Development Environment Setup

Before we start building, let's make sure you have all the necessary tools installed on your system.

### 1. Python Installation

#### Check if Python is already installed

```bash
python3 --version
```

If you see a version number (e.g., `Python 3.11.5`), you're good to go! If not, follow the installation steps below.

#### Install Python

**Windows (using WSL recommended):**
1. Open PowerShell as Administrator
2. Run: `wsl --install`
3. Restart your computer
4. After restart, Ubuntu will launch automatically
5. Update Ubuntu: `sudo apt update && sudo apt upgrade -y`
6. Install Python: `sudo apt install python3 python3-pip python3-venv -y`

**Linux (Ubuntu/Debian):**
```bash
sudo apt update
sudo apt install python3 python3-pip python3-venv -y
```

**macOS:**
```bash
# Using Homebrew (if you have it installed)
brew install python3

# Or download from python.org
```

#### Verify Python Installation

```bash
python3 --version
pip3 --version
```

### 2. Git Installation

#### Check if Git is already installed

```bash
git --version
```

#### Install Git

**Windows (WSL/Ubuntu):**
```bash
sudo apt install git -y
```

**Linux (Ubuntu/Debian):**
```bash
sudo apt install git -y
```

**macOS:**
```bash
# Using Homebrew
brew install git

# Or download from git-scm.com
```

#### Verify Git Installation

```bash
git --version
```

#### Configure Git

```bash
# Set your name and email
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Set default branch name
git config --global init.defaultBranch main

# Verify configuration
git config --list
```

### 3. GitHub Setup

#### Generate SSH Key for GitHub

```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "your.email@example.com"

# Press Enter to accept default file location
# Enter a passphrase or press Enter to skip (optional)
```

#### Display and Copy Your Public Key

```bash
# Display public key
cat ~/.ssh/id_ed25519.pub
```

Copy the entire output (it should start with `ssh-ed25519`).

#### Add SSH Key to GitHub

1. Go to [GitHub.com](https://github.com) and sign in
2. Click your profile picture → **Settings**
3. Click **"SSH and GPG keys"** in the left sidebar
4. Click **"New SSH key"**
5. Paste your public key and give it a title (e.g., "My Laptop")
6. Click **"Add SSH key"**

#### Test SSH Connection

```bash
ssh -T git@github.com
```

You should see: `Hi username! You've successfully authenticated...`

### 4. Create .gitignore File

Create a `.gitignore` file in your project root to exclude unnecessary files from version control:

```bash
# Create .gitignore file
cat > .gitignore << EOF
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
env/
venv/
ENV/
env.bak/
venv.bak/
.pytest_cache/

# IDEs
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Environment variables
.env

# Database
*.db
*.sqlite3

# Logs
logs/
*.log
EOF
```

Or manually create a `.gitignore` file with the above content.

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
