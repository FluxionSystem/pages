# FluxionSystem GitHub Pages — Template Rules

## Purpose

This template produces pages hosted on `pages.fluxionsystem.com` that look
visually identical to the main Webflow site at `fluxionsystem.com`. The header,
footer, fonts, colors, and spacing come from the same Webflow CSS. Only the
content area (`<main>`) changes per page.

---

## File naming and language structure

The Webflow site uses path-based locales. GitHub Pages must mirror this exactly.

### Convention

| Language | Webflow URL pattern          | GitHub Pages file path             | Live URL                                  |
|----------|------------------------------|------------------------------------|-------------------------------------------|
| EN       | `/pagename`                  | `/pagename/index.html`             | `pages.fluxionsystem.com/pagename/`       |
| UA       | `/ua/pagename`               | `/ua/pagename/index.html`          | `pages.fluxionsystem.com/ua/pagename/`    |
| IT       | `/it/pagename`               | `/it/pagename/index.html`          | `pages.fluxionsystem.com/it/pagename/`    |
| DE       | `/de/pagename`               | `/de/pagename/index.html`          | `pages.fluxionsystem.com/de/pagename/`    |

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
└── CNAME                   ← contains: pages.fluxionsystem.com
```

### Naming rules

- **Slug** = lowercase, hyphens only, no spaces, no underscores
  - Good: `denkware`, `case-study-aramis`, `press-brake-guide`
  - Bad: `DenkWare`, `case_study`, `my page`
- Same slug across all 4 languages (content is translated, URL slug stays the same)
- Every language version of a page is a **complete standalone HTML file** — not a partial, not a JS-switched variant
- A page does NOT need all 4 languages on day one. Start with EN, add others later. The language switcher will simply 404 for missing languages (acceptable for now).

---

## What to modify

### YES — modify these

- `<title>` and meta description
- Everything between `<!-- CONTENT START -->` and `<!-- CONTENT END -->`
- The `{{CUSTOM_CSS}}` area in the `<style>` block (for page-specific styles only)

### NO — do not modify these

- `<header>` block (entire thing, including nav and language dropdown)
- `<footer>` block (entire thing)
- The locale/navigation `<script>` at the bottom
- The Webflow CSS link
- The font loading `<link>` tags (do NOT use WebFont JS — it causes race conditions)
- Favicon links

---

## CRITICAL: CSS scoping rules

The `<main>` element MUST have the class `dw-page`:
```html
<main class="main gp-content dw-page">
```

The header and footer use Webflow's CSS. If your page CSS uses bare element
selectors, they WILL break the header/footer. This has happened and was painful
to debug. The rule is simple:

### NEVER use bare element selectors in page CSS

| WRONG (breaks header/footer)         | CORRECT (scoped to content)                    |
|---------------------------------------|------------------------------------------------|
| `nav { position: fixed; ... }`        | `.dw-subnav { position: sticky; ... }`         |
| `a { color: inherit; }`              | `.dw-page a { color: inherit; }`               |
| `h1, h2, h3 { font-family: ... }`   | `.dw-page h1, .dw-page h2, .dw-page h3 { }`  |
| `p { margin: 0; }`                   | `.dw-page p { margin: 0; }`                   |
| `footer { border-top: ... }`         | `.dw-page-footer { border-top: ... }`          |

**Why:** The Webflow header contains `<nav>`, `<a>`, `<div>` elements styled by
Webflow's CSS. A bare `nav {}` in your page CSS overrides the Webflow nav styling
and breaks the header layout, language dropdown, and logo positioning.

**The template already includes protection overrides** for `header.header` and
`footer.footer`, but these are a safety net — the real fix is scoping your CSS.

### If using a sub-navigation for in-page section links

Use class `.dw-subnav` (not bare `nav`), and `id="dw-nav"` (not `id="nav"`
which can conflict with page scripts):
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

## Available Webflow CSS classes

These classes come from the linked Webflow stylesheet and produce the exact same
visual result as on the main site. **Use these instead of writing custom CSS.**

### Layout
| Class             | Purpose                                      |
|-------------------|----------------------------------------------|
| `u-container`     | Centered max-width container with padding     |
| `u-section`       | Standard section with vertical padding         |
| `u-grid-autofit`  | Auto-fitting responsive grid                   |

### Typography
| Class                | Purpose                                     |
|----------------------|---------------------------------------------|
| `u-text-style-h1`   | Page title (hero heading)                    |
| `u-text-style-h2`   | Section heading                              |
| `u-text-style-large` | Large body text (hero subtitle)             |
| `u-text-style-main` | Standard body text                           |
| `u-text-style-small` | Fine print, captions                        |
| `text_accent`        | Teal accent color for inline spans           |
| `is--muted`          | Reduced opacity text                         |

### Width constraints
| Class     | Purpose                          |
|-----------|----------------------------------|
| `_w-580`  | Max-width ~580px (hero text)     |
| `_w-660`  | Max-width ~660px (body text)     |

### Components

**Section eyebrow** (small label above headings):
```html
<div class="section_eyebrow">
  <div class="eyebrow_bar"></div>
  <div class="eyebrow_text">Label Text</div>
</div>
```

**Primary button** (teal, filled):
```html
<div class="button_main_wrap">
  <div class="button_main_element">
    <div aria-hidden="true" class="button_main_text">Button Label</div>
  </div>
  <a aria-label="Button Label" href="/target" class="clickable_link w-inline-block"></a>
</div>
```

**Secondary button** (outlined):
```html
<div class="button_secondary_wrap">
  <div class="button_secondary_element">
    <div aria-hidden="true" class="button_main_text">Button Label</div>
  </div>
  <a aria-label="Button Label" href="/target" class="clickable_link w-inline-block"></a>
</div>
```

**Button group:**
```html
<div class="u-button-wrapper">
  <!-- primary button here -->
  <!-- secondary button here -->
</div>
```

---

## Color reference

| Token                    | Hex       | Use                              |
|--------------------------|-----------|----------------------------------|
| `--_colors---accent`     | `#17A589` | Primary teal (buttons, accents)  |
| `--_colors---accent-2`   | `#2E86C1` | Secondary blue                   |
| Background               | `#060E1A` | Page background (near-black)     |
| Text                     | `#FFFFFF` | Primary text                     |
| Muted text               | `rgba(255,255,255,0.65)` | Secondary text      |
| Hover teal               | `#127363` | Button hover state               |

---

## Navigation behavior

The template script handles this automatically:

- **Nav links** (Technology, Products, About, Contact) → point to `fluxionsystem.com` with the correct locale prefix
- **Language switcher** → switches between language versions on `pages.fluxionsystem.com` (same page slug, different locale prefix)
- **Logo** → links to `fluxionsystem.com/` (home)
- **Footer legal links** → point to `fluxionsystem.com/privacy-policy` etc.

When viewing the Ukrainian version at `pages.fluxionsystem.com/ua/denkware/`:
- "Products" links to `fluxionsystem.com/ua/products`
- "EN" in lang switcher links to `pages.fluxionsystem.com/denkware/`
- "DE" in lang switcher links to `pages.fluxionsystem.com/de/denkware/`

---

## Checklist before committing a new page

1. [ ] File is at the correct path (see naming convention above)
2. [ ] `<title>` and meta description are filled in
3. [ ] Only `<main>` content was modified — header/footer/script untouched
4. [ ] `<main>` has class `dw-page` (required for CSS scoping)
5. [ ] **No bare element selectors** in page CSS (`nav{}`, `a{}`, `h1{}`, `footer{}` etc.) — all scoped to `.dw-page`
6. [ ] Uses Webflow utility classes where possible, not custom duplicates
7. [ ] Any custom CSS is in the designated `{{CUSTOM_CSS}}` area
8. [ ] All internal links to the main site use absolute URLs (`https://fluxionsystem.com/...`)
9. [ ] Tested locally by opening the HTML file in a browser — header/footer intact, no console errors
