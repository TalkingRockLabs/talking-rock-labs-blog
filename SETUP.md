# Talking Rock Labs - Setup Guide

This guide will help you set up and deploy your new Talking Rock Labs blog to GitHub Pages.

## Quick Start

### 1. Local Development

First, install Jekyll and dependencies:

```bash
cd talking-rock-labs

# Install Ruby gems
bundle install

# Serve the site locally
bundle exec jekyll serve

# Visit http://localhost:4000 in your browser
```

### 2. Add Your Logo

Place your Talking Rock Labs logo image in the following location:
- Path: `public/images/talking-rock-labs-logo.png`
- Recommended size: 400x200px
- Format: PNG with transparent background

Until you add a logo, a placeholder will be displayed.

### 3. Update Configuration

Edit `_config.yml` to customize:
- `url`: Your GitHub Pages URL
- `baseurl`: Your repository base URL
- Update social media links in `_includes/sidebar.html`

### 4. Deploy to GitHub Pages

#### Option A: New Repository

```bash
# Initialize git repository
git init
git add .
git commit -m "Initial commit - Talking Rock Labs blog"

# Create a new repository on GitHub named "talking-rock-labs.github.io"
# Then push your code:
git remote add origin https://github.com/YOUR-USERNAME/talking-rock-labs.github.io.git
git branch -M main
git push -u origin main
```

#### Option B: Existing Repository

```bash
cd talking-rock-labs
git init
git add .
git commit -m "Initial commit - Talking Rock Labs blog"

git remote add origin https://github.com/talking-rock-labs/talking-rock-labs.github.io.git
git branch -M main
git push -u origin main
```

### 5. Enable GitHub Pages

1. Go to your repository on GitHub
2. Click "Settings"
3. Scroll down to "Pages" section
4. Under "Source", select the `main` branch
5. Click "Save"
6. Your site will be published at `https://talking-rock-labs.github.io`

### 6. Custom Domain (Optional)

If you have a custom domain:

1. Update the `CNAME` file with your domain name
2. Add DNS records pointing to GitHub Pages:
   ```
   A record: 185.199.108.153
   A record: 185.199.109.153
   A record: 185.199.110.153
   A record: 185.199.111.153
   ```
3. Wait for DNS propagation (can take up to 24 hours)

## Directory Structure

```
talking-rock-labs/
├── _config.yml           # Jekyll configuration
├── _includes/            # Reusable HTML components
│   ├── head.html
│   └── sidebar.html
├── _layouts/             # Page templates
│   ├── default.html
│   ├── page.html
│   └── post.html
├── _posts/               # Blog posts (Markdown)
├── public/
│   ├── css/              # Stylesheets
│   │   ├── main.css
│   │   └── syntax.css
│   └── images/           # Images and assets
├── about.md              # About page
├── contact.md            # Contact page
├── index.html            # Home page
└── 404.html              # 404 error page
```

## Creating New Posts

Create a new file in `_posts/` with the format: `YYYY-MM-DD-title.md`

Example: `2024-01-15-my-new-post.md`

```markdown
---
layout: post
title: "My New Post Title"
categories: security
published: true
date: 2024-01-15
---

Your post content goes here...
```

## Customization

### Colors

Edit CSS variables in `public/css/main.css`:

```css
:root {
  --primary-color: #2c3e50;
  --secondary-color: #3498db;
  --accent-color: #e74c3c;
  /* ... more variables */
}
```

### Navigation

Edit `_includes/sidebar.html` to modify:
- Navigation links
- Social media links
- Footer content

### About & Contact Pages

Edit the following files:
- `about.md` - Update researcher information
- `contact.md` - Add real contact information

## Updating Contact Information

Replace placeholder contact info in:
1. `contact.md` - Email addresses and social links
2. `_includes/sidebar.html` - GitHub link
3. `_config.yml` - Author information

## Blog Posts Included

The following posts from your personal blog have been migrated:
- 2020-05-07: Python AV Evasion
- 2020-07-19: Go Shellcode Fun
- 2020-12-12: Simple AD Attacks
- 2022-08-29: Writing Go Code

## Next Steps

1. Add your Talking Rock Labs logo to `public/images/`
2. Update `about.md` with complete researcher information
3. Replace placeholder contact information in `contact.md`
4. Customize colors and styling in `public/css/main.css`
5. Create your first Talking Rock Labs blog post
6. Deploy to GitHub Pages

## Troubleshooting

### Jekyll build errors
```bash
bundle exec jekyll build --verbose
```

### Clear cache
```bash
bundle exec jekyll clean
bundle exec jekyll serve
```

### Update dependencies
```bash
bundle update
```

## Support

For Jekyll documentation: https://jekyllrb.com/docs/
For GitHub Pages: https://docs.github.com/en/pages

---

Happy blogging from Talking Rock Labs!
