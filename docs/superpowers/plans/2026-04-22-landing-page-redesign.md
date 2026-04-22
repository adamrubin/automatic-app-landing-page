# Landing Page Redesign Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Transform the Jekyll landing-page template into a polished, modern, dark-themed site using a palette pulled from the app icon.

**Architecture:** Static Jekyll site. All changes are config + SCSS + one HTML include + one frontmatter flag. No new files, no removed files. Verification is visual in a browser (Jekyll auto-rebuilds on save).

**Tech Stack:** Jekyll (github-pages gem), SCSS, Inter web font from Google Fonts.

---

## Notes for the executor

- **No unit tests.** This is a visual design task. Each task ends with a browser verification step instead.
- **Keep the dev server running.** Start it once in Task 1 and leave it running; Jekyll auto-rebuilds SCSS on save.
- **Commit style** (matches recent repo history): short, lowercase, semicolon-separated.
- **The palette**:
  - Background: `#0b0618`
  - Hero gradient: `#3d1d6e` → `#0b0618` (radial, top-left origin)
  - Accent (gold): `#f5a742`
  - Text primary: `#ffffff`
  - Text secondary: `rgba(255,255,255,0.65)`
  - Text muted: `rgba(255,255,255,0.45)`
  - Divider: `rgba(255,255,255,0.08)`

---

## Task 1: Bootstrap dev server

**Files:**
- None modified

- [ ] **Step 1: Install dependencies**

Run from the repo root:

```bash
bundle install
```

Expected: `Bundle complete!` message. If Ruby/bundler isn't installed, stop and ask the user — they'll need to install it (typically `brew install ruby` on macOS, then `gem install bundler`).

- [ ] **Step 2: Start the Jekyll dev server**

Run (leave running in background for the rest of the tasks):

```bash
bundle exec jekyll serve --livereload
```

Expected: output includes `Server address: http://127.0.0.1:4000` and `Server running...`.

- [ ] **Step 3: Verify the site loads**

Open `http://localhost:4000` in a browser. Expected: current template renders with mountain/space hero, phone mockup, Support link in top-right.

---

## Task 2: Remove Support link from header

**Files:**
- Modify: `_pages/support.md` (frontmatter)

- [ ] **Step 1: Flip the header flag**

Edit `_pages/support.md`. Change:

```yaml
---
layout: page
title: Support
include_in_header: true
---
```

To:

```yaml
---
layout: page
title: Support
include_in_header: false
---
```

- [ ] **Step 2: Verify in browser**

Reload `http://localhost:4000`. Expected: the "Support" link in the top-right is gone. Header now shows only the app icon + app name on the left.

Reload `http://localhost:4000/support/`. Expected: Support page still accessible (routing is unchanged), and the footer still has the Support link.

- [ ] **Step 3: Commit**

```bash
git add _pages/support.md
git commit -m "remove support link from header"
```

---

## Task 3: Update config palette

**Files:**
- Modify: `_config.yml`

- [ ] **Step 1: Update theme values**

Edit `_config.yml`. Replace the entire `# Theme Settings` block (from `topbar_color` down to `footer_text_color`) with:

```yaml
# Theme Settings
topbar_color                              : "#0b0618"
topbar_transparency                       : 0
topbar_title_color                        : "#ffffff"

cover_image                               : assets/headerimage.png                    # Replace with alternative image path or image URL.
cover_overlay_color                       : "#0b0618"
cover_overlay_transparency                : 0

device_color                              : black                                     # Set to: blue, black, yellow, coral or white.

body_background_color                     : "#0b0618"

link_color                                : "#f5a742"

app_title_color                           : "#ffffff"
app_price_color                           : "#ffffff"
app_description_color                     : "#ffffff"

feature_title_color                       : "#ffffff"
feature_text_color                        : "rgba(255,255,255,0.65)"

feature_icons_foreground_color            : "#0b0618"
feature_icons_background_color            : "#f5a742"

social_icons_foreground_color             : "#0b0618"
social_icons_background_color             : "#f5a742"

footer_text_color                         : "rgba(255,255,255,0.45)"
```

- [ ] **Step 2: Restart Jekyll**

`_config.yml` changes require a server restart. Kill the running `jekyll serve` process (Ctrl+C in its terminal) and run:

```bash
bundle exec jekyll serve --livereload
```

- [ ] **Step 3: Verify in browser**

Reload `http://localhost:4000`. Expected:
- Hero still shows the old mountain image (gradient replaces it in Task 6)
- Body below the hero is now dark (`#0b0618`) — no white band
- Links on subpages (if visible in footer) are gold-orange (`#f5a742`)

Reload `http://localhost:4000/privacypolicy/`. Expected: header area and body background are still mostly white — we'll fix this in Tasks 5 and 6. Heading + body text still readable.

- [ ] **Step 4: Commit**

```bash
git add _config.yml
git commit -m "palette: dark theme with gold accent"
```

---

## Task 4: Load Inter from Google Fonts

**Files:**
- Modify: `_includes/head.html`

- [ ] **Step 1: Add preconnect + stylesheet link**

Edit `_includes/head.html`. Find this line:

```html
<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.6.3/css/all.css" integrity="sha384-UHRtZLI+pbxtHCWp1t77Bi1L4ZtiqrqD80Kn4Z8NTSRyMA2Fd33n5dQ8lWUE00s/" crossorigin="anonymous">
```

Insert these three lines **before** it:

```html
	<link rel="preconnect" href="https://fonts.googleapis.com">
	<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
	<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;800&display=swap">
```

(Tab indentation to match the surrounding file.)

- [ ] **Step 2: Verify in browser**

Reload `http://localhost:4000`. Open browser DevTools → Network tab → reload → filter by "Font". Expected: at least one `Inter` .woff2 request with status 200.

Visual appearance won't change yet — we haven't switched the font stack. That's Task 5.

- [ ] **Step 3: Commit**

```bash
git add _includes/head.html
git commit -m "load Inter from Google Fonts"
```

---

## Task 5: Base SCSS — font stack, remove subpage white, heading tracking

**Files:**
- Modify: `_sass/base.scss`

- [ ] **Step 1: Update the font stack**

Edit `_sass/base.scss`. Find:

```scss
$font: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
```

Replace with:

```scss
$font: "Inter", -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
```

- [ ] **Step 2: Remove the subpage white-body override**

In the same file, find and delete this block entirely:

```scss
.subPageBody {
	background-color: #fff;
}
```

- [ ] **Step 3: Add heading tracking**

In the same file, find the heading rules:

```scss
h1 {
	font-size: 3rem;
}

h2 {
	font-size: 2rem;
}

h3 {
    font-size: 2rem;
}
```

Replace with:

```scss
h1, h2, h3 {
	letter-spacing: -0.02em;
	font-weight: 800;
}

h1 {
	font-size: 3rem;
}

h2 {
	font-size: 2rem;
}

h3 {
    font-size: 2rem;
}
```

- [ ] **Step 4: Verify in browser**

Reload `http://localhost:4000`. Expected:
- Font is now Inter (sharper, more modern — app name in header reads crisper).
- Homepage unchanged otherwise.

Reload `http://localhost:4000/privacypolicy/`. Expected: body is now dark (the white body override is gone); text is probably unreadable (dark on dark) — we fix readability in Tasks 6 and 8.

- [ ] **Step 5: Commit**

```bash
git add _sass/base.scss
git commit -m "typography: Inter font, tighter heading tracking; drop subpage white"
```

---

## Task 6: Layout SCSS — hero gradient + drop subpage light-mode overrides

**Files:**
- Modify: `_sass/layout.scss`

- [ ] **Step 1: Replace the hero background with a CSS gradient**

Edit `_sass/layout.scss`. Find:

```scss
.imageWrapper {					// Sets the background image in the header area
	height: 714px;
	background: 
		linear-gradient(
		rgba($image-overlay-color, $image-overlay-transparency), 
		rgba($image-overlay-color, $image-overlay-transparency)
		   ),
		
		url($header-image);

	background-repeat: no-repeat;
	background-size: cover;
	background-position: top;
	border-radius: 0px 0px 40px 40px;

}
```

Replace with:

```scss
.imageWrapper {					// Hero gradient background
	height: 714px;
	background: radial-gradient(ellipse at top left, #3d1d6e 0%, #0b0618 70%);
	border-radius: 0px 0px 40px 40px;
}
```

- [ ] **Step 2: Drop the subpage white-header override**

In the same file, find and delete:

```scss
.subPageHeaderBackground {
	background-color: #fff;
}
```

- [ ] **Step 3: Drop the subpage container white background**

Find:

```scss
.subPageContainer {
	grid-template-rows: 115px auto auto auto;
	background-color: #fff;
}
```

Replace with:

```scss
.subPageContainer {
	grid-template-rows: 115px auto auto auto;
}
```

- [ ] **Step 4: Drop the subpage light-mode nav overrides**

Find and delete this entire block (the "Sub Page Header Styling" section):

```scss
// Sub Page Header Styling
.subPageHeaderBackground .logo > p {
	color: #000;
}

.subPageHeaderBackground nav > ul li a:link,
.subPageHeaderBackground nav > ul li a:visited {
	color: rgba(#000, 0.6);
}

.subPageHeaderBackground nav > ul li a:hover,
.subPageHeaderBackground nav > ul li a:active {
	color: rgba(#000, 1);
}
```

- [ ] **Step 5: Verify in browser**

Reload `http://localhost:4000`. Expected:
- Hero is now a purple radial gradient (no mountain image); rounded-bottom corners still present.
- Seamless transition from hero to body — no visible seam because both are dark.

Reload `http://localhost:4000/privacypolicy/` and `http://localhost:4000/support/`. Expected:
- Dark header matches homepage.
- App name in header reads white.
- Body is dark. Markdown text is still not readable (dark on dark) — Task 8 fixes that.

- [ ] **Step 6: Commit**

```bash
git add _sass/layout.scss
git commit -m "hero: css gradient; subpages: inherit dark theme"
```

---

## Task 7: Layout SCSS — load animation + App Store hover + nav hover accent

**Files:**
- Modify: `_sass/layout.scss`

- [ ] **Step 1: Update nav link transition and hover color**

Edit `_sass/layout.scss`. Find:

```scss
nav > ul li a:link,
nav > ul li a:visited {
	text-decoration: none;
	color: rgba($header-title-color, 0.6);
}

nav > ul li a:hover,
nav > ul li a:active {
	text-decoration: none;
	color: rgba($header-title-color, 1);
}
```

Replace with:

```scss
nav > ul li a:link,
nav > ul li a:visited {
	text-decoration: none;
	color: rgba($header-title-color, 0.6);
	transition: color 200ms cubic-bezier(0.2, 0.8, 0.2, 1);
}

nav > ul li a:hover,
nav > ul li a:active {
	text-decoration: none;
	color: $accent-color;
}
```

- [ ] **Step 2: Append the load animation + App Store hover at the end of the file**

Append to the end of `_sass/layout.scss`:

```scss


// Hero load animation
@keyframes riseIn {
	from { opacity: 0; transform: translateY(8px); }
	to { opacity: 1; transform: translateY(0); }
}

.logo,
.iphonePreview,
.appInfo {
	animation: riseIn 400ms cubic-bezier(0.2, 0.8, 0.2, 1) both;
}

.logo { animation-delay: 0ms; }
.iphonePreview { animation-delay: 120ms; }
.appInfo { animation-delay: 240ms; }

// App Store / Play Store button polish
.appStoreLink,
.playStoreLink {
	display: inline-block;
	transition: transform 200ms cubic-bezier(0.2, 0.8, 0.2, 1);
}

.appStoreLink:hover,
.playStoreLink:hover {
	transform: scale(1.02);
}

.appStoreLink img,
.playStoreLink img {
	transition: filter 200ms cubic-bezier(0.2, 0.8, 0.2, 1);
}

.appStoreLink:hover img,
.playStoreLink:hover img {
	filter: drop-shadow(0 0 24px rgba(245, 167, 66, 0.35));
}
```

- [ ] **Step 3: Verify in browser**

Reload `http://localhost:4000`. Expected:
- On load, the logo, phone, and App Store button fade in with a small upward rise, staggered ~120ms apart.
- Hover the App Store button: subtle scale-up + gold glow behind it.
- (If there were nav links) hover turns them gold — verify on privacy page footer link.

Reload a few times to confirm the animation feels snappy, not sluggish.

- [ ] **Step 4: Commit**

```bash
git add _sass/layout.scss
git commit -m "motion: hero load animation; button hover glow; nav accent hover"
```

---

## Task 8: Markdown dark theme for subpages

**Files:**
- Modify: `_sass/github-markdown.scss`

- [ ] **Step 1: Append dark-mode overrides**

Append to the end of `_sass/github-markdown.scss`:

```scss


// Dark mode overrides for subpages (RichMinds redesign)
.markdown-body {
	color: #ffffff;
	font-family: $font;
	font-size: 1.7rem;
	line-height: 1.6;
	background-color: transparent;
}

.markdown-body h1,
.markdown-body h2,
.markdown-body h3,
.markdown-body h4,
.markdown-body h5,
.markdown-body h6 {
	color: #ffffff;
	border-bottom-color: rgba(255, 255, 255, 0.08);
}

.markdown-body a {
	color: $accent-color;
}

.markdown-body a:hover {
	text-decoration: underline;
}

.markdown-body strong {
	color: #ffffff;
}

.markdown-body code,
.markdown-body pre {
	background-color: rgba(255, 255, 255, 0.05);
	color: #ffffff;
}

.markdown-body hr {
	background-color: rgba(255, 255, 255, 0.08);
	border-bottom-color: rgba(255, 255, 255, 0.08);
}

.markdown-body blockquote {
	color: rgba(255, 255, 255, 0.65);
	border-left-color: $accent-color;
}

.markdown-body table tr,
.markdown-body table th,
.markdown-body table td {
	background-color: transparent;
	border-color: rgba(255, 255, 255, 0.08);
}
```

- [ ] **Step 2: Verify in browser**

Reload `http://localhost:4000/privacypolicy/`. Expected:
- Body text is white, readable on the dark bg.
- "Privacy Policy" heading is large, white, Inter, tightened.
- Section headings (`## Information We Collect`, etc.) have a faint white underline divider.
- Any links in the page are gold; hover underlines.
- "Last updated" bold text is bright white.

Reload `http://localhost:4000/support/`. Expected: same treatment, reads clean.

- [ ] **Step 3: Commit**

```bash
git add _sass/github-markdown.scss
git commit -m "markdown: dark-mode overrides for subpages"
```

---

## Task 9: Final QA pass

**Files:**
- None (verification only, possibly tiny tweaks)

- [ ] **Step 1: Desktop full-site walkthrough**

In a browser at full desktop width (≥1200px), visit in order:
- `http://localhost:4000` — hero gradient, phone, App Store button, fade-in animation runs once, footer links gold
- `http://localhost:4000/privacypolicy/` — dark theme, Inter, readable markdown
- `http://localhost:4000/support/` — same

Check for: any stray white strip, unreadable text, gold-on-gold (both shouldn't be possible given the palette), broken alignment.

- [ ] **Step 2: Mobile viewport check**

Open DevTools → device toolbar → iPhone 12 Pro (390px) or similar. Visit each page. Expected:
- Hero shrinks, phone centers, no horizontal scroll.
- Header nav scrolls cleanly with only the app icon + divider visible if nav items exist.
- Subpages still readable; margins reasonable.

- [ ] **Step 3: Fix any regressions**

If anything looks broken, fix inline and include in a final commit. Common issues to watch for:
- Footer `Made by...` text — only renders if `your_name` is set in config; it isn't, so no action needed.
- Any lingering light-mode bg — search the repo for `#fff` or `#ffffff` background rules: `grep -rn "background-color: *#f" _sass/`.

- [ ] **Step 4: Final commit (only if tweaks were needed)**

```bash
git add -A
git commit -m "polish: final redesign cleanup"
```

If no tweaks were needed, skip this step.

- [ ] **Step 5: Stop the dev server**

Ctrl+C in the terminal running `jekyll serve`.

---

## Completion criteria

- [ ] All 9 tasks committed.
- [ ] Homepage: purple→dark gradient hero, dark body, no white band, animated on load.
- [ ] Subpages: dark theme, Inter, readable markdown with gold accents.
- [ ] Support link in footer only (not header).
- [ ] App Store button has hover glow.
- [ ] No browser console errors.
