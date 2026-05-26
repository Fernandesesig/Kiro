# Tech Stack

## Overview
This is a **single-file vanilla web prototype** — no build system, no package manager, no framework.

- **HTML5** — single `index.html` file contains all markup, styles, and scripts
- **CSS** — inline `<style>` block, no preprocessors
- **JavaScript** — inline `<script>` blocks, vanilla ES5/ES6, no transpilation
- **Font Awesome 6.5** — loaded via CDN for icons

## Architecture
Everything lives in `index.html`:
- Styles in `<style>` in `<head>`
- Menu data as a JS array (`var menu = [...]`)
- UI rendering and interaction logic in `<script>` blocks at the bottom of `<body>`
- Page content rendered dynamically into `#content` div via `innerHTML`

## No Build / No Commands
There is no build step. Open `index.html` directly in a browser or serve with any static file server:

```bash
# Option 1: direct open
start index.html

# Option 2: simple static server (Python)
python -m http.server 8080

# Option 3: simple static server (Node)
npx serve .
```

## External Dependencies (CDN only)
- Font Awesome: `https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css`
