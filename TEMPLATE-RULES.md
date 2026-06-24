# FluxionSystem GitHub Pages — Template Rules v2

## Architecture

Pages are hosted on `pages.fluxionsystem.com` via GitHub Pages.
The template includes header, footer, fonts, and language switching inline —
**no external Webflow CSS is loaded**. This was a deliberate decision after
Webflow's CSS (`body{display:flex}`) caused an unfixable iOS Safari scroll-lock.

All header/footer styles are defined inline in the `<style>` block.

---

## File naming and language structure

The Webflow site uses path-based locales. GitHub Pages mirrors this exactly.

| Language | GitHub Pages file path             | Live URL                                  |
|----------|------------------------------------|-------------------------------------------|
| EN       | `/pagename/index.html`             | `pages.fluxionsystem.com/pagename/`       |
| UA       | `/ua/pagename/index.html`          | `pages.fluxionsystem.com/ua/pagename/`    |
| IT       | `/it/pagename/index.html`          | `pages.fluxionsystem.com/it/pagename/`    |
| DE       | `/de/pagename/index.html`          | `pages.fluxionsystem.com/de/pagename/`    |

### Example: a page called "denkware"

```
repo root/
├── denkware/
│   └── index.html          ← English version
├── ua/
│   └── denkware/
│       └── index.html      ← Ukrainian version
├── it/
│   └── denkware/
│       └── index.html      ← Italian version
├── de/
│   └── denkware/
│       └── index.html      ← German version
├── TEMPLATE.html            ← Copy this to start a new page
├── TEMPLATE-RULES.md        ← This file
└── CNAME                    ← contains: pages.fluxionsystem.com
```

### Naming rules

- **Slug** = lowercase, hyphens only, no spaces, no underscores
  - Good: `denkware`, `case-study-aramis`, `press-brake-guide`
  - Bad: `DenkWare`, `case_study`, `my page`
- Same slug across all 4 languages (content translated, URL stays the same)
- Every language version is a complete standalone HTML file
- A page does NOT need all 4 languages on day one — start with EN, add others later

---

## What to modify

### YES — modify these

- `<title>` and meta description (replace `{{PAGE_TITLE}}` etc.)
- Everything inside `<main class="main gp-content dw-page">` ... `</main>`
- The `{{CUSTOM_CSS}}` area in the `<style>` block

### NO — do not modify these

- The `<header class="header">` block
- The `<footer class="footer">` block
- The locale/navigation `<script>` at the bottom
- The inline header/footer CSS at the top of `<style>`
- The font `<link>` tags
- Favicon links

---

## CRITICAL: CSS scoping rules

The `<main>` element MUST have the class `dw-page`:
```html
<main class="main gp-content dw-page">
```

### NEVER use bare element selectors in page CSS

| WRONG (breaks header/footer)         | CORRECT (scoped to content)                    |
|---------------------------------------|------------------------------------------------|
| `nav { position: fixed; ... }`        | `.dw-subnav { position: sticky; ... }`         |
| `a { color: inherit; }`              | `.dw-page a { color: inherit; }`               |
| `h1, h2, h3 { font-family: ... }`   | `.dw-page h1, .dw-page h2 { ... }`            |
| `p { margin: 0; }`                   | `.dw-page p { margin: 0; }`                   |
| `footer { border-top: ... }`         | `.dw-page-footer { border-top: ... }`          |
| `body { overflow-x: hidden }`        | `.dw-page { overflow-x: hidden }`             |

**Why:** The header and footer use classes like `.header`, `.nav`, `.footer_*` etc.
If your page CSS targets bare HTML elements (`nav`, `a`, `footer`), it overrides
the inline header/footer styles and breaks layout.

### NEVER add external Webflow CSS

Do NOT add a `<link>` to Webflow's stylesheet. It sets `body{display:flex}`
which locks scrolling on iOS Safari. This was discovered the hard way.

### Sub-navigation for in-page section links

Use class `.dw-subnav`, not bare `nav`:
```html
<nav class="dw-subnav" id="dw-nav">
  <div class="dw-subnav-inner">
    <div class="navlinks">
      <a href="#section-1">Section 1</a>
      <a href="#section-2">Section 2</a>
    </div>
  </div>
</nav>
```

---

## Color reference

| Color              | Hex / Value                    | Use                              |
|--------------------|--------------------------------|----------------------------------|
| Teal accent        | `#17A589`                      | Primary accent (buttons, highlights) |
| Blue accent        | `#2E86C1`                      | Secondary accent                 |
| Background         | `#060E1A`                      | Page background (near-black)     |
| Text               | `#FFFFFF`                      | Primary text                     |
| Muted text         | `rgba(255,255,255,0.65)`       | Secondary/body text              |
| Hover teal         | `#127363`                      | Button hover state               |

---

## Navigation behavior

The template's locale script handles this automatically:

- **Nav links** (Technology, Products, About, Contact) → point to `fluxionsystem.com` with correct locale prefix
- **Language switcher** → switches between language versions on `pages.fluxionsystem.com`
- **Logo** → links to `fluxionsystem.com/`
- **Footer legal links** → point to `fluxionsystem.com/privacy-policy` etc.

---

## Known iOS Safari issues (solved)

These were encountered and solved during development. Do not reintroduce them:

1. **`body{display:flex}`** — Webflow's external CSS sets this. Locks scroll on iOS. Fix: don't load Webflow CSS; override with `body{display:block!important}`.
2. **`html{height:100%}`** — Webflow sets this. Limits scroll height to viewport. Fix: `html{height:auto!important}`.
3. **`overflow-x:hidden` on body** — Can prevent vertical scroll on iOS. Fix: put it on `.dw-page` wrapper instead.
4. **Canvas without `pointer-events:none`** — Full-screen `<canvas>` elements intercept touch events. Fix: add `pointer-events:none;touch-action:none`.
5. **Bare `nav{}` CSS** — Overrides the header's `<nav>` element. Fix: scope to `.dw-subnav`.

---

## Checklist before committing a new page

1. [ ] File is at the correct path (see naming convention above)
2. [ ] `<title>` and meta description are filled in
3. [ ] Only `<main>` content was modified — header/footer/script untouched
4. [ ] `<main>` has class `dw-page`
5. [ ] **No bare element selectors** in page CSS — all scoped to `.dw-page`
6. [ ] **No Webflow external CSS link** added
7. [ ] `body { overflow-x: hidden }` NOT used (use `.dw-page` instead)
8. [ ] Canvas/overlay elements have `pointer-events: none`
9. [ ] All links to the main site use absolute URLs (`https://fluxionsystem.com/...`)
10. [ ] Tested on mobile (iPhone Safari) — page scrolls, header/footer intact
