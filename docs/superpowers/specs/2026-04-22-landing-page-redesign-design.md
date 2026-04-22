# Landing Page Redesign

## Goal

Transform the current landing page for **RichMinds - Predict the Story** from an off-the-shelf Jekyll template into a polished, modern, dark-themed single-page site (plus subpages) with a cohesive aesthetic pulled from the app icon.

## Problems with the current state

1. Support link appears in both the header and the footer (redundant).
2. The homepage has a white band below the hero (body background shows through under the hero's rounded corners).
3. Subpages (privacy policy, support) flip to an all-white theme, breaking consistency with the dark homepage.
4. Overall visual quality is low — generic system font, flat gray overlay over a stock mountain photo, and no design personality.

## Aesthetic direction

Pull the palette from the app icon (purple-to-pink gradient with a warm gold chart line). Apply a dark theme consistently across all pages, with gold as the single accent color. Modernize typography with Inter. Keep motion restrained.

### Palette

| Token | Value | Usage |
|---|---|---|
| `--bg` | `#0b0618` | Body background (all pages) |
| `--hero-from` | `#3d1d6e` | Hero gradient start (top-left) |
| `--hero-to` | `#0b0618` | Hero gradient end (bottom-right) |
| `--accent` | `#f5a742` | Links, App Store button glow, accent dividers |
| `--text-primary` | `#ffffff` | Headings, body copy |
| `--text-secondary` | `rgba(255,255,255,0.65)` | Nav links, muted copy |
| `--text-muted` | `rgba(255,255,255,0.45)` | Footer |
| `--divider` | `rgba(255,255,255,0.08)` | Markdown `hr`, subtle separators |

### Typography

- Load **Inter** from Google Fonts (weights: 400, 600, 800).
- Update the `$font` SCSS variable in `_sass/base.scss` to put Inter first in the stack.
- Headings: 800 weight, `letter-spacing: -0.02em`.
- Body: 400 weight, `line-height: 1.6` in markdown contexts.

### Motion

All transitions ≤ 400ms, easing `cubic-bezier(0.2, 0.8, 0.2, 1)`.

- On-load: staggered fade + 8px translateY on hero logo, app name, phone, App Store button.
- App Store button hover: scale 1.02, soft `box-shadow: 0 0 24px rgba(245,167,66,0.35)`.
- Nav link hover: color fades from `--text-secondary` → `--accent`.

Explicitly out of scope: parallax, scroll-linked animations, starfield, custom logo work.

## Implementation units

The changes split into six units, each with clear boundaries. The palette tokens above (`--bg`, `--accent`, etc.) are conceptual names — actual implementation uses the existing SCSS variable pipeline (`_config.yml` → `main.scss` → partials). No CSS custom properties are introduced.

### 1. Config palette (`_config.yml`)

Update theme values to the new palette. The template already reads these into SCSS variables, so most color changes happen here with no code changes.

- `topbar_color: "#0b0618"`, `topbar_transparency: 0` (solid match to hero bottom)
- `topbar_title_color: "#ffffff"`
- `cover_overlay_color: "#0b0618"`, `cover_overlay_transparency: 0` (overlay no longer used — gradient replaces image)
- `body_background_color: "#0b0618"` (fixes issue #2)
- `link_color: "#f5a742"`
- `app_title_color`, `app_price_color`, `app_description_color`: `"#ffffff"`
- `feature_title_color: "#ffffff"`, `feature_text_color: "rgba(255,255,255,0.65)"`
- `footer_text_color: "rgba(255,255,255,0.45)"`
- `social_icons_foreground_color: "#0b0618"`, `social_icons_background_color: "#f5a742"`

### 2. Header link removal (`_pages/support.md`)

Flip `include_in_header: true` → `false`. Footer still renders it via the `site.pages` loop in `_includes/footer.html`. Fixes issue #1.

### 3. Typography load (`_includes/head.html`)

Add Google Fonts preconnect + stylesheet link for Inter (400/600/800). Single `<link>` injection; no other head changes.

### 4. Base styles (`_sass/base.scss`)

- Prepend `"Inter"` to `$font` stack.
- Delete the `.subPageBody { background-color: #fff }` rule (subpages inherit the dark body).
- Add heading tracking: `h1, h2, h3 { letter-spacing: -0.02em; font-weight: 800; }`.
- Keep existing resets.

### 5. Layout styles (`_sass/layout.scss`)

- Replace `.imageWrapper` background: remove the stock-photo + overlay, use a pure CSS radial gradient: `radial-gradient(ellipse at top left, #3d1d6e 0%, #0b0618 70%)`. Keep the 40px bottom radius.
- Remove `.subPageHeaderBackground { background-color: #fff }` override — subpage header now inherits the transparent-over-dark treatment.
- Remove `.subPageContainer { background-color: #fff }`.
- Delete the four "Sub Page Header Styling" rules that force nav text to black on subpages (they now inherit the light-on-dark nav from the homepage rules).
- Add App Store button hover state (scale + glow).
- Add `@keyframes` + staggered `animation-delay` on hero children for the on-load fade/rise.

### 6. Markdown dark theme (`_sass/github-markdown.scss`)

The `github-markdown.scss` partial provides GitHub-flavored markdown styling for subpage content. Currently it's light-theme. Append a targeted dark-mode override block at the end, scoped under `.markdown-body` (existing class on the `<article>` in `_layouts/page.html`):

- Body text: white, `font-size: 1.7rem`, `line-height: 1.6`.
- H1/H2/H3: white, keep size hierarchy; restyle the existing H2 border to `rgba(255,255,255,0.08)`.
- Links: gold accent (`#f5a742`), hover underline.
- `code`, `pre`: subtle dark fill (`rgba(255,255,255,0.05)`).
- `hr`: `rgba(255,255,255,0.08)`.
- `blockquote`: muted text + left border in gold accent.

Homepage unaffected since it doesn't use `.markdown-body`.

Note: `headerimage.png` stays in the repo unreferenced in case it's wanted later. No files deleted.

## Files touched

| File | Type of change |
|---|---|
| `_config.yml` | Palette values |
| `_pages/support.md` | Frontmatter flag |
| `_includes/head.html` | Add Google Fonts link |
| `_sass/base.scss` | Font stack, remove `.subPageBody` white, heading tracking |
| `_sass/layout.scss` | Replace hero bg with gradient, remove subpage white overrides, remove subpage nav overrides, add animations + hover |
| `_sass/github-markdown.scss` | Append dark-mode overrides under `.markdown-body` |

No new files. No removed files.

## Verification

Jekyll site — verify visually in a browser:

1. `bundle exec jekyll serve` and open `http://localhost:4000`.
2. **Homepage**: dark purple-radial hero → seamless dark body below (no white band). Phone mockup reads clearly. On-load animation runs once. App Store button has hover glow.
3. **Header**: shows only app icon + name (left) and nothing on the right (Support link gone).
4. **Subpages** (`/privacypolicy/`, `/support/`): same dark theme, white body text, gold headings or dividers, readable line length.
5. **Mobile (≤768px)**: existing responsive rules still work; hero stacks, nav scrolls, colors consistent.
6. **Links**: gold color, hover fade.

## Out of scope

- Changing page copy.
- Replacing App Store PNG with a custom button.
- Changing the app icon or adding new assets.
- Adding a starfield, parallax, or scroll animations.
- Modifying the Jekyll collection structure or URLs.
