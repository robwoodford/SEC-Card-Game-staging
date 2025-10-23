# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Jekyll static website for the Scottish Episcopal Church Net Zero Toolkit and Card Game. The site uses the Just the Docs theme and dynamically generates most pages from an Excel spreadsheet using Python scripts and Liquid templating.

**Site URL**: https://robwoodford.github.io/SEC-Card-Game-staging

## Development Commands

### Local Development Server
```bash
bundle exec jekyll serve -w --baseurl '' --port 4000
```
Note: The `--baseurl ''` flag is required for graphics to render correctly during local development.

### Content Generation from Spreadsheet

The site content is generated from an Excel file using Python. This process is currently manual:

1. Open the master spreadsheet in Excel
2. Copy all cells on each tab and paste values in place (to replace formulas with values)
3. Save as `aux/master-spreadsheet.xlsx`
4. Create and activate Python virtual environment (if not already done):
   ```bash
   python -m venv card-game-venv
   source card-game-venv/bin/activate
   python -m pip install openpyxl pandas
   ```
5. Generate the website content:
   ```bash
   cd aux
   python generate-website.py
   rm -rf ../collections/*
   mv output/* ../collections
   cd ..
   ```

## Architecture

### Collections Structure

The site is organized around 6 Jekyll collections that represent a hierarchical content model:

- **Petals** (`_petals`): Top-level categories (e.g., "Energy Efficiency Improvements")
- **Steps** (`_steps`): Major phases within petals (e.g., "Basic Actions")
- **Tasks** (`_tasks`): Specific tasks within steps (e.g., "Maintain the building")
- **Cards** (`_cards`): Individual action cards with guidance (53 cards total)
- **Links** (`_links`): External resources linked to cards (187 links)
- **Tags** (`_tags`): Metadata tags for classification

Navigation hierarchy: Petal → Step → Task → Card

Only the Petals collection appears in site navigation; others are accessed through custom includes and breadcrumbs.

### Content Generation System

The `aux/generate-website.py` script reads sheets from `master-spreadsheet.xlsx` and applies string templates to generate markdown files:

- Each collection has a template file in `aux/` (e.g., `cards-template.txt`)
- Templates use Python's `string.Template` syntax with `$variable` placeholders
- Each row in a spreadsheet sheet generates one markdown file
- Files are numbered sequentially (0.md, 1.md, etc.)

### Custom Layouts and Includes

**Layouts** (`_layouts/`):
- `card.html` - Renders individual cards with icons, colors, quotes, cost/carbon ratings
- `petal.html`, `step.html`, `task.html` - Render collection pages
- All layouts use Liquid templating to cross-reference collections

**Key Includes** (`_includes/`):
- `breadcrumbs.html` - Navigation breadcrumbs showing petal → step → task hierarchy
- `cards-in-task.html` - Lists all cards for a task
- `steps-in-petal.html` - Lists all steps within a petal
- `links.html` - Renders external resource links for cards

### Static Content

Hand-written markdown pages in the root directory cover supplementary content:
- `index.md` - Homepage
- `reading-a-card.md`, `playing_the_game.md` - Instructions
- `workshops.md` - Workshop information
- `net-zero-*.md` - Background information on net zero concepts

### Graphics and Assets

- `/graphics/icons/` - SVG icons for cards, steps, and tags
- `/graphics/card_icons/` - Additional card-specific icons
- `/graphics/site-logo.png` - Site logo
- `/css/` - Custom CSS overrides for Just the Docs theme

## Configuration

**_config.yml** key settings:
- `baseurl: "."` - Set to empty string during local development
- Collections are defined with output settings
- Just the Docs configuration hides most collections from navigation
- Callouts configured for highlight, important, new, note, warning

## Important Notes

- **Graphics paths**: Always use `{{ "/path/to/file" | relative_url }}` for graphics to work in both local and GitHub Pages environments
- **Formula values**: The spreadsheet must have formulas replaced with values before generation (current manual step)
- **Excluded directories**: `aux/`, `junk/`, and virtual environment directories are excluded from Jekyll builds
- The project was built quickly and the README notes it's "a mess" needing rationalization for easier updates

## Known Issues and Future Improvements

From README.md:
- Automating the Excel formula-to-values conversion in Python
- Making site builds automatic
- Improving non-technical updateability of the toolkit
- CSS rationalization
- Magic wand icon sizing and coloring issues
