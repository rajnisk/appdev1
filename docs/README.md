# Bootcamp Tutorials - Technical Blog

A technical documentation site built with Jekyll and the [Just the Docs](https://just-the-docs.github.io/just-the-docs/) theme, perfect for GitHub Pages.

## 🚀 Quick Start

### Local Development

1. **Install Ruby** (if not already installed)
   ```bash
   # Ubuntu/Debian
   sudo apt-get install ruby-full build-essential
   
   # macOS (using Homebrew)
   brew install ruby
   ```

2. **Install Bundler**
   ```bash
   gem install bundler
   ```

3. **Install dependencies**
   ```bash
   cd docs
   bundle install
   ```

4. **Run locally**
   ```bash
   bundle exec jekyll serve
   ```

5. **View the site**
   Open `http://localhost:4000` in your browser

## 🌐 Deploy to GitHub Pages

### Method 1: Using GitHub Actions (Recommended)

1. Push this `docs` folder to a GitHub repository

2. Go to **Settings** → **Pages**

3. Under **Source**, select **GitHub Actions**

4. Create `.github/workflows/jekyll.yml`:
   ```yaml
   name: Deploy Jekyll site to Pages

   on:
     push:
       branches: ["main"]
     workflow_dispatch:

   permissions:
     contents: read
     pages: write
     id-token: write

   concurrency:
     group: "pages"
     cancel-in-progress: false

   jobs:
     build:
       runs-on: ubuntu-latest
       steps:
         - name: Checkout
           uses: actions/checkout@v4
         - name: Setup Ruby
           uses: ruby/setup-ruby@v1
           with:
             ruby-version: '3.1'
             bundler-cache: true
             working-directory: docs
         - name: Setup Pages
           uses: actions/configure-pages@v4
         - name: Build with Jekyll
           run: bundle exec jekyll build --baseurl "${{ steps.pages.outputs.base_path }}"
           working-directory: docs
           env:
             JEKYLL_ENV: production
         - name: Upload artifact
           uses: actions/upload-pages-artifact@v3
           with:
             path: docs/_site

     deploy:
       environment:
         name: github-pages
         url: ${{ steps.deployment.outputs.page_url }}
       runs-on: ubuntu-latest
       needs: build
       steps:
         - name: Deploy to GitHub Pages
           id: deployment
           uses: actions/deploy-pages@v4
   ```

### Method 2: Deploy from Branch

1. Push the `docs` folder contents to a `gh-pages` branch

2. Go to **Settings** → **Pages**

3. Under **Source**, select the `gh-pages` branch

## 📁 Structure

```
docs/
├── _config.yml          # Jekyll configuration
├── Gemfile              # Ruby dependencies
├── index.md             # Home page
├── day0-setup.md        # Day 0 content
├── day1-backend.md      # Day 1 content
├── day2-restful.md      # Day 2 content
├── day3-frontend.md     # Day 3 content
├── day4-vue-advanced.md # Day 4 content
├── day5-advanced.md     # Day 5 content
├── about.md             # About page
└── README.md            # This file
```

## ✏️ Customization

### Update Site Info

Edit `_config.yml`:
```yaml
title: Your Site Title
description: Your site description
url: https://yourusername.github.io
baseurl: "/your-repo-name"
```

### Add New Pages

1. Create a new `.md` file
2. Add front matter:
   ```yaml
   ---
   title: Page Title
   layout: default
   nav_order: 9
   ---
   ```

### Customize Theme

See [Just the Docs documentation](https://just-the-docs.github.io/just-the-docs/) for:
- Color schemes
- Custom CSS
- Navigation structure
- Search configuration

## 📝 Writing Content

### Code Blocks

```markdown
```python
def hello():
    print("Hello, World!")
```
```

### Callouts

```markdown
{: .note }
This is a note callout.

{: .warning }
This is a warning callout.

{: .important }
This is an important callout.
```

### Tables

```markdown
| Header 1 | Header 2 |
|----------|----------|
| Cell 1   | Cell 2   |
```

## 📄 License

MIT License - feel free to use this template for your own projects!
