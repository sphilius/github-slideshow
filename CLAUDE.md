# CLAUDE.md - AI Assistant Guide for github-slideshow

**Last Updated**: 2025-11-16
**Repository**: github-slideshow
**Purpose**: Guide for AI assistants working with this codebase

---

## 🎯 Project Overview

This is a **Jekyll-based slideshow presentation** using reveal.js, created for GitHub Learning Lab training. It demonstrates Git/GitHub workflows through an interactive presentation interface.

**Technology Stack**:
- Jekyll (static site generator)
- reveal.js v3.9.2 (presentation framework)
- Ruby/Bundler (dependency management)
- html-proofer (HTML validation)

**Primary Use Case**: Educational tool for GitHub Learning Lab courses teaching version control

---

## 📁 Codebase Structure

### Directory Layout

```
github-slideshow/
│
├── _config.yml              # Jekyll configuration + reveal.js settings
│   ├── Site metadata (title, author, description)
│   ├── Jekyll settings (markdown, highlighter, plugins)
│   └── reveal.js initialization config
│
├── _posts/                  # 📝 SLIDE CONTENT (markdown files)
│   └── 0000-01-01-intro.md  # Slides use date-based naming: YYYY-MM-DD-title.md
│                             # Older dates appear first in presentation
│
├── _layouts/                # 🎨 HTML TEMPLATES
│   ├── presentation.html    # Main reveal.js wrapper
│   ├── slide.html          # Individual slide layout
│   └── print.html          # Print/PDF export layout
│
├── _includes/               # 🧩 REUSABLE COMPONENTS
│   ├── head.html           # <head> metadata, CSS links
│   ├── script.html         # reveal.js initialization script
│   └── slide.html          # Slide section template
│
├── script/                  # 🔧 DEVELOPMENT SCRIPTS
│   ├── setup               # Initial environment setup
│   ├── server              # Start Jekyll dev server
│   ├── cibuild             # Build + validate for CI
│   └── stage               # Staging deployment
│
├── node_modules/            # 📦 NPM DEPENDENCIES
│   └── reveal.js/          # Presentation framework (v3.9.2)
│
├── index.html              # 🏠 ENTRY POINT (iterates through posts)
├── Gemfile                 # Ruby dependencies
├── Gemfile.lock            # Locked Ruby dependency versions
├── package-lock.json       # Locked npm dependency versions
└── README.md               # User-facing documentation
```

---

## 🏗️ Architecture & Data Flow

### How Slides Are Generated

1. **Markdown → HTML Conversion**
   ```
   _posts/YYYY-MM-DD-name.md
   ↓ (Jekyll processes frontmatter + content)
   ↓ (Applied to _layouts/slide.html)
   ↓ (Wrapped in _layouts/presentation.html)
   → HTML <section> element with reveal.js classes
   ```

2. **Slide Post Structure**
   ```markdown
   ---
   layout: slide              # Must be 'slide'
   title: "Slide Title"       # Displayed as <h1>
   ---

   Slide content in **markdown**
   ```

3. **Build Process**
   ```
   script/setup               # Install dependencies
   ↓
   bundle exec jekyll build   # Generate _site/ directory
   ↓
   reveal.js renders slides   # Client-side presentation
   ```

### Key Files Explained

| File | Purpose | When to Modify |
|------|---------|----------------|
| `_config.yml` | Jekyll + reveal.js settings | Changing site metadata, reveal.js behavior |
| `_posts/*.md` | Individual slide content | Adding/editing slides |
| `_layouts/presentation.html` | reveal.js container | Major structural changes |
| `_includes/head.html` | CSS/meta tags | Adding stylesheets, fonts |
| `_includes/script.html` | reveal.js initialization | Changing presentation behavior |
| `index.html` | Loops through posts | Modifying slide order logic |
| `script/server` | Dev server launcher | Changing server options |

---

## 🛠️ Development Workflows

### Common Tasks

#### 1. Adding a New Slide

```bash
# Create new markdown file in _posts/
# Date determines order (older = earlier in presentation)
cat > _posts/0000-01-02-new-slide.md << 'EOF'
---
layout: slide
title: "My New Slide"
---

This is my slide content with **markdown** support!

- Bullet points work
- Code blocks work
- Images work
EOF

# Preview locally
script/server
# Visit http://localhost:4000
```

**Naming Convention**: `YYYY-MM-DD-descriptive-name.md`
- Year/month/day control order (earlier dates = earlier slides)
- Common pattern: increment day for each slide (0000-01-01, 0000-01-02, etc.)

#### 2. Modifying Presentation Behavior

**Location**: `_includes/script.html` (lines 7-16)

```javascript
Reveal.initialize({
  hash: true,              // URL hash navigation
  dependencies: [...]       // Plugins (markdown, highlight, notes)
});
```

**Note**: Settings in `_includes/script.html` override `_config.yml`

#### 3. Changing Theme/Styling

**CSS Theme**: `_includes/head.html` (line 10)
```html
<link rel="stylesheet" href="{{site.baseurl}}/node_modules/reveal.js/css/theme/moon.css">
```

Available themes in `node_modules/reveal.js/css/theme/`:
- `black.css`, `white.css`, `league.css`, `beige.css`, `sky.css`,
- `night.css`, `serif.css`, `simple.css`, `solarized.css`, `moon.css`

**Syntax Highlighting**: `_includes/head.html` (line 13)
```html
<link rel="stylesheet" href="{{site.baseurl}}/node_modules/reveal.js/lib/css/monokai.css">
```

#### 4. Building for Production

```bash
# Build site
bundle exec jekyll build --baseurl "."

# Validate HTML
htmlproofer _site/index.html --empty-alt-ignore

# Or use the combined script
script/cibuild
```

Output: `_site/` directory (ready for deployment)

#### 5. Local Development

```bash
# First time setup
script/setup

# Start dev server (with live reload)
script/server

# Or manually
bundle exec jekyll serve
```

Access at `http://localhost:4000`

---

## 🧭 Navigation & Conventions

### Git Workflow

**Branch Naming**: `claude/descriptive-name-SESSION_ID`
- All Claude development happens on dedicated branches
- Never push directly to main/master

**Commit Messages**:
```bash
# Good examples from history
"Bump nokogiri from 1.10.8 to 1.11.1"
"Comment out baseurl"
"fix: Gemfile & Gemfile.lock to reduce vulnerabilities"

# Follow conventional commits format
fix: brief description       # Bug fixes
feat: brief description      # New features
docs: brief description      # Documentation
chore: brief description     # Maintenance
```

### File Naming Conventions

| Type | Pattern | Example |
|------|---------|---------|
| Slide posts | `YYYY-MM-DD-slug.md` | `0000-01-01-intro.md` |
| Layouts | `name.html` | `presentation.html` |
| Includes | `name.html` | `slide.html` |
| Scripts | `lowercase-name` (no extension) | `setup`, `server` |

### Code Style

**Ruby (Gemfile)**:
- Single quotes for strings
- One gem per line
- Group related gems
- Specify version constraints (`>=`)

**Liquid Templates** (Jekyll):
- Use `{% %}` for logic/control flow
- Use `{{ }}` for output
- Indent nested blocks
- Add whitespace control with `{%-` to prevent blank lines

**Markdown** (Slides):
- ATX-style headers (`#` not underlines)
- Fenced code blocks with language (` ```ruby `)
- Reference-style links for readability

---

## 🔍 Important Implementation Details

### Jekyll Configuration Nuances

1. **baseurl is Commented Out** (`_config.yml:4`)
   - Allows site to work both locally and on GitHub Pages
   - Build scripts use `--baseurl "."` for local builds
   - Be cautious when uncommenting

2. **Timezone Matters** (`_config.yml:1`)
   - Set to `Europe/Berlin`
   - Affects post date interpretation
   - May impact build reproducibility across regions

3. **Future Posts Disabled** (`_config.yml:2`)
   - `future: false` hides posts with future dates
   - Useful for staging unpublished slides

### reveal.js Integration

1. **Dependency Loading** (`_includes/script.html`)
   - Uses deprecated v3.x `dependencies` array
   - Loads: marked.js (markdown), markdown.js, highlight.js, notes.js
   - **Warning**: This syntax is obsolete in reveal.js v5+

2. **Asset Paths**
   - All paths use `{{ site.baseurl }}` prefix
   - Points to `node_modules/reveal.js/`
   - Relies on npm package, NOT Git submodule

3. **Slide Section Generation** (`_includes/slide.html`)
   - Dynamically adds CSS classes from post frontmatter
   - Supports custom `slide-id` for anchor links
   - Allows `data-*` attributes for reveal.js transitions

### Build & Validation

1. **html-proofer Settings** (`script/cibuild:6`)
   - `--empty-alt-ignore`: Allows images without alt text
   - Only validates `index.html` (not all generated pages)
   - Catches broken links, invalid HTML

2. **Setup Script Logic** (`script/setup`)
   - Checks for Homebrew on macOS
   - Installs Ruby via rbenv if `.ruby-version` exists
   - Runs `bundle install --no-cache`
   - **Line 28**: Runs `git submodule update --init` (now redundant)

---

## ⚠️ Known Issues & Gotchas

### Critical Issues

1. **No package.json Exists**
   - Only `package-lock.json` is tracked
   - Running `npm install` may create inconsistent package.json
   - **Fix**: Create proper package.json (see UPDATES.md)

2. **Outdated reveal.js**
   - Current: v3.9.2 (2020)
   - Latest: v5.x (2024)
   - Breaking changes in v4+ (module system, plugin loading)

3. **Deprecated Plugin Loading**
   - `dependencies` array is legacy syntax
   - Modern reveal.js uses ES6 imports
   - Will break on upgrade without migration

### Minor Issues

4. **Submodule Cleanup Incomplete**
   - `.gitmodules` exists but is empty
   - `script/setup:28` still runs submodule update
   - **Fix**: Remove both

5. **Dual Configuration**
   - `_config.yml` has reveal.js settings
   - `_includes/script.html` has different settings
   - Script settings take precedence (can cause confusion)

6. **Limited Slide Content**
   - Only one example slide exists
   - Doesn't showcase reveal.js features (vertical slides, fragments, etc.)

---

## 🤖 AI Assistant Guidelines

### When Modifying This Codebase

#### DO:
✅ **Read existing slides** before adding new ones to match style
✅ **Test builds locally** with `script/server` before committing
✅ **Follow date-based naming** for slide posts
✅ **Update _config.yml** when changing site-wide settings
✅ **Validate HTML** with `script/cibuild` before pushing
✅ **Use markdown** for slide content (not raw HTML unless necessary)
✅ **Preserve existing conventions** (layout structure, naming patterns)
✅ **Check dependencies** are up-to-date (but test thoroughly)

#### DON'T:
❌ **Modify reveal.js source files** in node_modules (use config instead)
❌ **Commit node_modules/** to git (managed by npm)
❌ **Push to main branch** (use claude/* branches)
❌ **Break existing slides** when refactoring templates
❌ **Ignore build warnings** from Jekyll or html-proofer
❌ **Mix configuration** between _config.yml and script.html arbitrarily
❌ **Use inline styles** (use reveal.js classes or themes)
❌ **Forget frontmatter** (layout: slide is required)

### Making Changes Safely

**Before Any Modification**:
1. Run `script/server` to verify current state works
2. Read related files to understand context
3. Check git history for why things are configured as-is

**When Adding Slides**:
1. Copy existing slide post as template
2. Adjust date to control ordering
3. Test in browser (reveal.js keyboard: arrow keys, 'O' for overview)
4. Verify markdown renders correctly

**When Updating Dependencies**:
1. Update Gemfile or package-lock.json
2. Run `bundle update` or `npm install`
3. Test build with `script/cibuild`
4. Check for deprecation warnings
5. Test presentation in browser (all features)

**When Changing Layouts**:
1. Understand the template hierarchy
2. Test with multiple slide variations
3. Verify print layout still works
4. Check responsive behavior

### Debugging Common Problems

| Problem | Likely Cause | Solution |
|---------|--------------|----------|
| Slides not appearing | Incorrect frontmatter | Verify `layout: slide` exists |
| Wrong slide order | Date in filename | Older dates appear first |
| CSS not loading | baseurl misconfiguration | Check `_config.yml:4` and build command |
| reveal.js not working | Script loading error | Check browser console, verify node_modules/ exists |
| Build fails | Ruby version mismatch | Run `script/setup` to install correct Ruby |
| htmlproofer errors | Broken links/invalid HTML | Check _site/index.html, fix source |

### Testing Checklist

Before committing changes:
- [ ] `script/server` runs without errors
- [ ] Slides display correctly in browser
- [ ] Navigation works (arrow keys, click)
- [ ] `script/cibuild` passes validation
- [ ] No console errors in browser DevTools
- [ ] Print layout works (`?print-pdf` URL parameter)
- [ ] All new slides have proper frontmatter
- [ ] Commit message follows conventional format

---

## 📚 Essential Commands Reference

```bash
# Setup & Installation
script/setup                          # First-time setup (Ruby, gems, dependencies)
bundle install                        # Install/update Ruby gems
npm install                           # Install/update npm packages

# Development
script/server                         # Start dev server (http://localhost:4000)
bundle exec jekyll serve              # Alternative dev server command
bundle exec jekyll serve --livereload # With auto-reload on changes

# Building
script/cibuild                        # Build + validate (CI-ready)
bundle exec jekyll build              # Build only (outputs to _site/)
bundle exec jekyll build --baseurl "." # Build with local paths

# Testing & Validation
htmlproofer _site/index.html --empty-alt-ignore  # Validate HTML
bundle exec jekyll doctor             # Check for deprecations/issues

# Dependency Management
bundle update                         # Update all gems
bundle update nokogiri                # Update specific gem
gem list                              # Show installed gems
npm list reveal.js                    # Check reveal.js version

# Git Operations
git log --oneline --graph            # View commit history
git diff                             # See uncommitted changes
git status                           # Check working directory
```

---

## 🔗 Key Dependencies

### Ruby Gems (Gemfile)

```ruby
gem 'github-pages', '>= 207'      # GitHub Pages Jekyll environment
gem 'html-proofer', '>= 3.13.0'   # HTML validation tool
gem 'tzinfo-data'                 # Timezone data (Windows)
```

**Includes** (via github-pages):
- jekyll (~> 3.9)
- jekyll-sitemap
- jekyll-seo-tag
- jemoji (emoji support)
- kramdown (markdown processor)
- rouge (syntax highlighter)

### NPM Packages (package-lock.json)

```json
"reveal.js": "3.9.2"   # Presentation framework
```

**Includes** (in reveal.js):
- marked.js (markdown rendering)
- highlight.js (code syntax highlighting)
- Plugin system (notes, zoom, search)

---

## 🎓 Learning Resources

### reveal.js Documentation
- **Homepage**: https://revealjs.com/
- **GitHub**: https://github.com/hakimel/reveal.js
- **v3.x Docs**: https://github.com/hakimel/reveal.js/tree/3.9.2#readme
- **Configuration**: https://revealjs.com/config/
- **Markdown**: https://revealjs.com/markdown/

### Jekyll Documentation
- **Homepage**: https://jekyllrb.com/
- **Posts**: https://jekyllrb.com/docs/posts/
- **Frontmatter**: https://jekyllrb.com/docs/front-matter/
- **Liquid**: https://shopify.github.io/liquid/
- **GitHub Pages**: https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll

### GitHub Learning Lab
- **Original Context**: https://lab.github.com/
- **Course Design**: Hands-on, bot-guided tutorials
- **This Repo's Role**: Interactive example for Git/GitHub lessons

---

## 🚀 Quick Start for AI Assistants

### First Time Working on This Repo

1. **Understand the context**
   ```bash
   cat README.md          # User-facing docs
   cat UPDATES.md         # Current status report
   cat _config.yml        # Configuration overview
   ```

2. **Explore the structure**
   ```bash
   ls _posts/             # See existing slides
   cat _posts/0000-01-01-intro.md   # Example slide
   cat index.html         # Entry point logic
   ```

3. **Test the current state**
   ```bash
   script/setup           # Ensure dependencies installed
   script/server          # Verify it works
   # Visit http://localhost:4000 in browser
   ```

4. **Review recent changes**
   ```bash
   git log --oneline -10  # Recent commits
   git status             # Current state
   ```

### Common AI Tasks

**Task: Add a slide about feature X**
1. Read existing slides to match style
2. Create `_posts/YYYY-MM-DD-feature-x.md`
3. Add frontmatter (layout, title)
4. Write content in markdown
5. Test with `script/server`
6. Commit with message: `feat: add slide about feature X`

**Task: Update reveal.js**
1. Check current version: `npm list reveal.js`
2. Review migration guide for new version
3. Update package-lock.json
4. Modify `_includes/script.html` for API changes
5. Update CSS paths in `_includes/head.html`
6. Test thoroughly with `script/server` and `script/cibuild`
7. Commit with message: `chore: update reveal.js to vX.Y.Z`

**Task: Fix build error**
1. Read error message carefully
2. Check Jekyll doctor: `bundle exec jekyll doctor`
3. Validate syntax in modified files
4. Test incrementally with `script/server`
5. Consult debugging table in this guide

**Task: Improve documentation**
1. Read existing docs (README.md, this file)
2. Identify gaps or outdated info
3. Update with current, accurate information
4. Verify examples actually work
5. Commit with message: `docs: improve documentation for X`

---

## 📞 Getting Help

### For AI Assistants

**When stuck**:
1. Re-read this CLAUDE.md file thoroughly
2. Check UPDATES.md for known issues
3. Review Jekyll/reveal.js official docs
4. Examine git history for similar changes
5. Test in isolation (create minimal reproduction)

**Before asking user**:
- Verify you've checked all relevant documentation
- Explain what you've tried
- Provide specific error messages
- Suggest possible solutions

### For Humans

**Resources**:
- Jekyll Docs: https://jekyllrb.com/docs/
- reveal.js Docs: https://revealjs.com/
- GitHub Pages: https://docs.github.com/en/pages
- GitHub Learning Lab: https://lab.github.com/

**Reporting Issues**:
- Include Jekyll version: `bundle exec jekyll --version`
- Include Ruby version: `ruby --version`
- Provide full error message
- Share steps to reproduce

---

## 📝 Version History

| Date | Changes | Author |
|------|---------|--------|
| 2025-11-16 | Initial CLAUDE.md creation | Claude (AI Assistant) |

---

**End of Guide**

This document should be updated whenever significant architectural changes occur or new patterns emerge. Treat it as living documentation that evolves with the codebase.
