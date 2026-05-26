# Project Structure

## File Layout
```
/
├── index.html          # Entire application (markup + styles + scripts)
└── .kiro/
    ├── specs/
    │   └── sigrh-prototype/
    │       ├── requirements.md
    │       ├── design.md
    │       └── tasks.md
    └── steering/
        ├── product.md
        ├── tech.md
        └── structure.md
```

## index.html Internal Organization

| Section | Location | Purpose |
|---|---|---|
| `<style>` | `<head>` | All CSS — layout, components, color tokens |
| `#header` | `<body>` | Top bar with logo, nav icons, user button |
| `#sidebar` | `#layout` | Collapsible 3-level menu (module → submodule → feature) |
| `#main` | `#layout` | Content area; shows `#home` or `#content` |
| Modals | After `#layout` | Overlay dialogs (`.modal-overlay`) |
| `<script>` blocks | End of `<body>` | Menu data array, rendering functions, page actions |

## UI Conventions

- **Color palette**: primary blue `#5b83b2`, dark blue `#4a72a1`, light blue `#b8d4ec`, accent yellow `#f0c020`
- **Font size**: base `12px`, labels `11px`
- **Menu levels**: module (`.m-item`, yellow text) → submodule (`.s-item`, dark text) → feature (`.ss-item`)
- **Page structure**: `.page-title` bar + `.page-body` with `.frow`/`.fg` form groups and `table.grid` data grids
- **Buttons**: `.btn` + modifier (`.btn-green`, `.btn-blue`, `.btn-gray`, `.btn-red`)
- **Sections**: `.alt-section` / `.section-box` with a colored header and padded body
- **Tabs**: `.tabs` container with `.tab` items; active tab shows `.tab-body.active`

## Adding New Pages

New functionality is added by:
1. Adding an entry to the `menu` JS array with an `action` string
2. Defining a JS function matching that action name that calls `showPage(title, htmlContent)`
3. All page HTML is built as a template string inside the JS function
