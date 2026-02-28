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

**Windows:**
```cmd
python --version
```
or
```cmd
py --version
```

**Linux/macOS:**
```bash
python3 --version
```

If you see a version number (e.g., `Python 3.11.5`), you're good to go! If not, follow the installation steps below.

#### Install Python

**Windows:**
1. Go to [python.org/downloads](https://www.python.org/downloads/)
2. Download the latest Python 3.x installer for Windows
3. Run the installer
4. **Important:** Check the box "Add Python to PATH" during installation
5. Click "Install Now"
6. Verify installation by opening Command Prompt or PowerShell and running:
   ```cmd
   python --version
   pip --version
   ```

**Alternative for Windows (Microsoft Store):**
1. Open Microsoft Store
2. Search for "Python 3.11" or "Python 3.12"
3. Click "Install"

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

**Windows:**
```cmd
python --version
pip --version
```

**Linux/macOS:**
```bash
python3 --version
pip3 --version
```

### 2. Git Installation

#### Check if Git is already installed

**Windows:**
```cmd
git --version
```

**Linux/macOS:**
```bash
git --version
```

#### Install Git

**Windows:**
1. Go to [git-scm.com/download/win](https://git-scm.com/download/win)
2. Download the latest Git for Windows installer
3. Run the installer
4. Use the default options (recommended)
5. After installation, open Command Prompt or PowerShell and verify:
   ```cmd
   git --version
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

**Windows:**
```cmd
git --version
```

**Linux/macOS:**
```bash
git --version
```

#### Configure Git

**Windows (Command Prompt or PowerShell):**
```cmd
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
git config --global init.defaultBranch main
git config --list
```

**Linux/macOS:**
```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
git config --global init.defaultBranch main
git config --list
```

### 3. GitHub Setup

#### Generate SSH Key for GitHub

**Windows (Git Bash or PowerShell):**
```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "your.email@example.com"

# Press Enter to accept default file location
# Enter a passphrase or press Enter to skip (optional)
```

**Linux/macOS:**
```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "your.email@example.com"

# Press Enter to accept default file location
# Enter a passphrase or press Enter to skip (optional)
```

#### Display and Copy Your Public Key

**Windows (Git Bash):**
```bash
cat ~/.ssh/id_ed25519.pub
```

**Windows (PowerShell):**
```powershell
type $env:USERPROFILE\.ssh\id_ed25519.pub
```

**Linux/macOS:**
```bash
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

**Windows (Git Bash or PowerShell):**
```bash
ssh -T git@github.com
```

**Linux/macOS:**
```bash
ssh -T git@github.com
```

You should see: `Hi username! You've successfully authenticated...`

### 4. Create .gitignore File

Create a `.gitignore` file in your project root to exclude unnecessary files from version control:

**Windows (PowerShell):**
```powershell
@"
# Python
__pycache__/
*.py[cod]
*`$py.class
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
"@ | Out-File -FilePath .gitignore -Encoding utf8
```

**Windows (Git Bash) or Linux/macOS:**
```bash
cat > .gitignore << EOF
# Python
__pycache__/
*.py[cod]
*\$py.class
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

**Or manually create a `.gitignore` file** in your project root with the above content.

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
