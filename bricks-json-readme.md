# Bricks Builder Clipboard JSON README

## Purpose

Use this guide when generating pasteable Bricks Builder clipboard JSON for WordPress/Bricks sites.

The goal is to create Bricks-native, editable elements that can be copied from ChatGPT and pasted directly into Bricks Builder. Do **not** output HTML/CSS-only layouts unless explicitly asked. Output Bricks clipboard JSON.

---

## 1. AI Quick Start

### Default approach

Use this as the default generation strategy:

1. Build native Bricks elements.
2. Use direct element settings rather than trying to recreate Brixies/global-class-heavy exports.
3. Use native Bricks layout/style controls wherever possible before reaching for CSS.
4. Use existing site variables where known.
5. Add BEM-style `_cssClasses` for scoped CSS/JS hooks.
6. Use Bricks global classes for repeated editable items and repeated behaviour where bulk editing will help.
7. Include code elements only when needed, and scope all CSS/JS to the section/form.
8. Keep all element IDs exactly 6 characters long.
9. Validate parent/child relationships before output.

### Preferred response style

When asked for a Bricks section or component:

1. If the user asks for design direction, create mockup options first.
2. Once the design is approved, output one complete Bricks clipboard JSON blob.
3. Put the blob in a fenced `json` code block.
4. Keep explanation minimal.
5. Mention likely post-paste edits only where useful, such as swapping images, updating links, or adjusting hotspot positions.

### When not to rebuild the whole JSON

For large existing structures, especially BricksForge Pro Forms, avoid rebuilding the entire blob unless the user explicitly wants that.

Safer alternatives:

- Replace one row or card group at a time.
- Replace one step in a multi-step form.
- Replace only a hidden-fields block.
- Provide only settings keys/values to add manually.
- Provide only webhook data mappings.
- Provide only updated JS or PDF HTML.

Large BricksForge forms contain functional IDs, calculations, conditional logic, PDF merge tags, submit/download actions and webhook mappings. Rebuilding the whole form is high-risk.

---

## 2. Clipboard JSON Format

### Required wrapper

Every Bricks clipboard JSON blob should follow this structure:

```json
{
  "content": [
    { "element": "objects go here" }
  ],
  "source": "bricksCopiedElements",
  "sourceUrl": "https://example.com",
  "version": "2.3.4"
}
```

Optional keys:

- `globalClasses`: use for repeated editable items that need native bulk editing.
- `globalElements`: usually not needed.

### Element object shape

```json
{
  "id": "abc123",
  "name": "section",
  "parent": 0,
  "children": ["def456"],
  "settings": {},
  "label": "Readable label"
}
```

Rules:

- `id` must be unique within the blob.
- `id` must be exactly 6 characters long.
- Use labels for readability, not descriptive IDs.
- Top-level sections use `"parent": 0`.
- Every child ID listed in `children` must exist.
- Every non-root element must have a valid parent ID.
- Keep one main section per blob unless asked for multiple sections.
- For complex sections with code, put code elements as direct section children after the main container.
- If code elements are included, their IDs must be included in the section’s `children` array.

### Common element names

Common native Bricks elements:

- `section`
- `container`
- `block`
- `div`
- `heading`
- `text-basic`
- `text`
- `button`
- `image`
- `icon`
- `svg`
- `code`
- `search`
- `toggle`
- `offcanvas`
- `form`

BricksForge Pro Forms elements are covered separately below.

---

## 3. Critical ID Rules

### Hard rule

Every Bricks element ID must be exactly **6 alphanumeric characters**.

Good:

```text
ab1001
hero01
ft12aa
m1a2b3
```

Bad:

```text
mobileToggle
megaPanel
footerItem01
fy1024a
```

### Why this matters

Observed failure:

- Parent blocks imported.
- Child elements silently disappeared.
- Empty wrapper blocks appeared in Bricks.
- This happened most often in footers, trust items, contact items, CTA rows and nested card structures.
- Cause: child IDs longer than 6 characters.
- Regenerating the same structure with strict 6-character IDs fixed it.

### ID validation checklist

Before outputting JSON, verify:

- Every element ID is exactly 6 characters.
- Every ID is unique.
- Every `children` reference matches a real element ID.
- Every non-root `parent` points to an existing element.
- Attribute IDs in `_attributes` are short and unique, ideally 6 characters.
- Form field IDs are also short where possible.
- Readability lives in `label`, not `id`.

---

## 4. Output Validation Checklist

Before delivering a JSON blob, mentally run this checklist:

- Clipboard wrapper is valid: `content`, `source`, `sourceUrl`, `version`.
- Top-level element has `parent: 0`.
- All IDs are exactly 6 characters.
- All child IDs exist.
- No invalid parent references.
- No missing code element IDs in section children.
- CSS/JS selectors are scoped.
- Image URLs are sensible for the target site.
- Repeated cards are real card containers, not loose text elements.
- Icons are present if the mockup expects icons.
- Mobile breakpoints are included where relevant.
- JSON is complete and pasteable.

Success criteria:

The user should be able to copy the entire JSON blob, paste it into Bricks, and get a native editable section. Interactive blobs should still show sensible static content if JS fails.

---

## 5. Styling Strategy

### Direct settings first

Direct Bricks element settings import more reliably than trying to recreate heavy external systems.

Best default approach:

- Native Bricks elements.
- Direct settings on those elements.
- Existing site variables where available.
- Native Bricks controls for layout, spacing, alignment, typography, borders, backgrounds, shadows and responsive behaviour wherever practical.
- Optional scoped CSS/JS code elements only when the behaviour cannot be created cleanly with native controls.
- BEM-style classes for local hooks.
- Bricks global classes for repeated editable elements only where useful.

### Avoid unnecessary CSS code blocks

Strong preference: do **not** use a `code` element or a CSS block for styling that can be handled by native Bricks controls.

Use native Bricks settings for:

- grid and flex layouts
- column counts and responsive breakpoints
- padding, margin, gap and alignment
- colours, backgrounds, borders and border radius
- typography and text alignment
- box shadows
- element widths, max-widths and aspect ratios
- simple show/hide breakpoint behaviour

A CSS code element is appropriate when:

- Bricks does not expose the needed control.
- A pseudo-class or pseudo-element is needed.
- A small interaction effect is needed, such as hover lift.
- A plugin/form element needs selector-based styling.
- JavaScript needs stable class hooks.
- The CSS would otherwise have to be repeated manually across many elements.

Before adding a CSS code block, ask: “Could this be done with Bricks settings on the element itself?” If yes, use Bricks settings.

### BEM classes + Bricks global classes

Recommended pattern:

- Section root: `section-name-v1`
- Elements: `section-name-v1__card`, `section-name-v1__title`, `section-name-v1__icon-wrap`
- Modifiers: `section-name-v1__button--dark`

Use BEM classes for section-specific CSS/JS hooks.

Use Bricks global classes for repeated elements the user may want to bulk-edit natively:

- card titles
- card body text
- icon wrappers
- process cards
- service cards
- repeated buttons
- repeated section headings

Example:

```json
{
  "settings": {
    "_cssClasses": "why-choose-v2__process-title",
    "_cssGlobalClasses": ["gcptit"]
  }
}
```

Bricks quirk: when editing a class in Bricks, the user must select the global class pill first. Otherwise changes may save as element-level overrides.

### Repeated hover/interaction styling with global classes

For repeated card effects, prefer adding a shared class to every repeated card rather than writing ID-specific CSS for each one.

Proven pattern:

1. Add the same class to every card, e.g. `gp-hover-lift-card`.
2. Add the CSS once in the Custom CSS field of one card, the section, or a small scoped code element.
3. Target the class directly.

Example card setting:

```json
{
  "settings": {
    "_cssClasses": "gp-hover-lift-card"
  }
}
```

Example CSS:

```css
.gp-hover-lift-card {
  transition: transform 0.24s ease, box-shadow 0.24s ease;
  will-change: transform;
}

.gp-hover-lift-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 30px 72px -56px rgba(0, 0, 0, 0.34);
}
```

This was tested successfully in Bricks: applying the class to all cards and placing the class-targeted CSS in one element’s Custom CSS field applied the hover effect to all elements with that class.

Use this for small repeatable behavioural styling such as:

- hover lift cards
- hover image zoom wrappers
- repeated route buttons
- repeated icon cards
- repeated feature cards

Avoid ID-specific selectors like `#brxe-abc123:hover` for repeated styling unless only one element should receive the effect.

### Element Custom CSS and `%root%`

Bricks element-level Custom CSS can target the element wrapper with `%root%`.

Use `%root%` when the CSS should apply only to that one element:

```css
%root% {
  transition: transform 0.24s ease, box-shadow 0.24s ease;
  will-change: transform;
}

%root%:hover {
  transform: translateY(-4px);
}
```

Use a shared class when the same CSS should apply to multiple elements:

```css
.shared-card-class {
  transition: transform 0.24s ease, box-shadow 0.24s ease;
}

.shared-card-class:hover {
  transform: translateY(-4px);
}
```

Preferred order for hover effects:

1. If one-off: use `%root%` in that element’s Custom CSS.
2. If repeated: add a shared class to all repeated elements and target the class once.
3. If section-wide/plugin styling: use a scoped code element only when necessary.

### Common site variables

Prefer target-site variables where known.

Typography:

```text
var(--brxw-text-xs)
var(--brxw-text-s)
var(--brxw-text-m)
var(--brxw-text-l)
var(--brxw-text-xl)
var(--brxw-text-2xl)
var(--brxw-text-3xl)
var(--brxw-text-4xl)
var(--brxw-text-5xl)
```

Spacing:

```text
var(--brxw-space-3xs)
var(--brxw-space-2xs)
var(--brxw-space-xs)
var(--brxw-space-s)
var(--brxw-space-m)
var(--brxw-space-l)
var(--brxw-space-xl)
var(--brxw-space-2xl)
var(--brxw-space-3xl)
var(--brxw-space-4xl)
var(--brxw-space-5xl)
```

Layout:

```text
var(--brxw-section-space-horizontal)
var(--brxw-section-space-vertical)
var(--brxw-container-gap)
var(--brxw-grid-gap)
var(--brxw-content-gap)
var(--brxw-container-width)
```

Grids:

```text
var(--brxw-grid-1)
var(--brxw-grid-2)
var(--brxw-grid-3)
var(--brxw-grid-4)
var(--brxw-grid-2-3)
var(--brxw-grid-3-2)
```

Radii:

```text
var(--brxw-radius-xs)
var(--brxw-radius-s)
var(--brxw-radius-m)
var(--brxw-radius-l)
var(--brxw-radius-xl)
var(--brxw-radius-full)
```

Ratios:

```text
var(--brxw-ratio-widescreen)
var(--brxw-ratio-square)
var(--brxw-ratio-landscape)
```

Common colours:

```text
var(--brxw-color-neutral-25)
var(--brxw-color-neutral-50)
var(--brxw-color-neutral-100)
var(--brxw-color-neutral-200)
var(--brxw-color-neutral-300)
var(--brxw-color-neutral-400)
var(--brxw-color-neutral-500)
var(--brxw-color-neutral-600)
var(--brxw-color-neutral-700)
var(--brxw-color-neutral-800)
var(--brxw-color-neutral-900)
var(--brxw-color-neutral-950)
var(--brxw-color-neutral-trans-10)
var(--brxw-color-overlay)
```

Generic fallback tokens when no design system is confirmed:

```text
Primary accent: var(--brand-primary)
Primary hover: var(--brand-primary-hover)
Soft surface: var(--surface-soft)
White/surface: var(--surface-white)
Text/ink: var(--text-primary)
```

If the target site does not already define these tokens, replace them with that site’s own confirmed variables or colours before generating final JSON.

---

## 6. Core Element Patterns

### Heading

```json
{
  "id": "hd1001",
  "name": "heading",
  "parent": "pa1001",
  "children": [],
  "settings": {
    "text": "Heading text",
    "tag": "h2",
    "_typography": {
      "font-size": "var(--brxw-text-3xl)",
      "font-weight": "800",
      "line-height": "1.12",
      "color": {"raw": "var(--brxw-color-neutral-950)"}
    }
  },
  "label": "Heading"
}
```

### Text-basic

```json
{
  "id": "tx1001",
  "name": "text-basic",
  "parent": "pa1001",
  "children": [],
  "settings": {
    "text": "Paragraph text",
    "tag": "p",
    "_typography": {
      "font-size": "var(--brxw-text-m)",
      "line-height": "1.7",
      "color": {"raw": "var(--brxw-color-neutral-trans-80)"}
    }
  },
  "label": "Description"
}
```

### HTML text block

Use `text` for HTML lists or deliberately structured HTML.

```json
{
  "id": "html01",
  "name": "text",
  "parent": "pa1001",
  "children": [],
  "settings": {
    "text": "<ul class=\"custom-list\"><li>Item one</li><li>Item two</li></ul>"
  },
  "label": "List"
}
```

### Text rule for structured mini-content

Avoid complex inline HTML such as `<br><span>...</span>` inside `text-basic` when building structured items. Bricks can render or import this unpredictably.

Good pattern:

```text
Trust item
  icon
  block
    heading
    text-basic

Contact item
  icon
  block
    heading
    text-basic

CTA copy
  heading
  text-basic
```

### Button

```json
{
  "id": "btn001",
  "name": "button",
  "parent": "pa1001",
  "children": [],
  "settings": {
    "text": "Explore products",
    "style": "primary",
    "icon": {"library": "fontawesomeSolid", "icon": "fas fa-arrow-right"},
    "iconPosition": "right",
    "link": {"type": "external", "url": "#", "ariaLabel": "Explore products"},
    "_background": {"color": {"raw": "var(--brand-primary)"}},
    "_typography": {
      "font-size": "var(--brxw-text-s)",
      "font-weight": "800",
      "text-transform": "uppercase",
      "letter-spacing": "0.04em",
      "color": {"raw": "var(--brxw-color-neutral-25)"}
    },
    "_border": {
      "radius": {
        "top": "var(--brxw-radius-s)",
        "right": "var(--brxw-radius-s)",
        "bottom": "var(--brxw-radius-s)",
        "left": "var(--brxw-radius-s)"
      }
    }
  },
  "label": "Button"
}
```

### Image

Use real site media-library image objects where available.

```json
{
  "id": "img001",
  "name": "image",
  "parent": "pa1001",
  "children": [],
  "settings": {
    "tag": "figure",
    "caption": "none",
    "image": {
      "id": 123,
      "filename": "placeholder-image.jpg",
      "size": "large",
      "full": "https://example.com/wp-content/uploads/placeholder-image.jpg",
      "url": "https://example.com/wp-content/uploads/placeholder-image-1024x576.jpg"
    },
    "_width": "100%",
    "_height": "100%",
    "_objectFit": "cover",
    "_objectPosition": "50%",
    "loading": "lazy"
  },
  "label": "Image"
}
```

For interactive image tracks where all images should be immediately available, use `"loading": "eager"`.

### Relative image and link URLs

When possible, use relative WordPress media URLs instead of staging-domain absolute URLs, especially for sites moving from staging to live.

Preferred:

```json
{
  "full": "/wp-content/uploads/YYYY/MM/file.webp",
  "url": "/wp-content/uploads/YYYY/MM/file.webp"
}
```

For internal links:

```json
{
  "type": "internal",
  "url": "/contact/",
  "ariaLabel": "Get in touch",
  "postId": "138"
}
```

If the target page does not exist yet, use the planned slug, such as `/product-category/`, `/service-page/`, or `/contact/`.

### SVG and icon rules

Use `svg` for logos or deliberate SVG placeholders.

For small icons, prefer native Bricks `icon` elements with FontAwesome. Empty SVG placeholders can render as large “No SVG selected” boxes and disrupt layouts.

Reliable native icon:

```json
{
  "id": "ic1001",
  "name": "icon",
  "parent": "pa1001",
  "children": [],
  "settings": {
    "icon": {"library": "fontawesomeSolid", "icon": "fas fa-leaf"},
    "iconSize": "24",
    "iconColor": {"raw": "var(--brand-primary)"},
    "_cssClasses": "section__icon"
  },
  "label": "Icon"
}
```

If the mockup includes icons, include icons in the JSON. If a close FontAwesome icon is not obvious, insert a placeholder SVG inside a wrapper div/block. Size the wrapper, not the SVG itself.

Good icon slot structure:

```text
Card
  Icon wrapper div
    native icon OR placeholder SVG
  Heading
  Text
```

---

## 7. Layout Patterns

### Grid container

```json
{
  "_display": "grid",
  "_gridTemplateColumns": "var(--brxw-grid-2)",
  "_gridTemplateColumns:tablet_portrait": "var(--brxw-grid-1)",
  "_gridGap": "var(--brxw-grid-gap)",
  "_alignItemsGrid": "center"
}
```

### Three-column panel

```json
{
  "_display": "grid",
  "_gridTemplateColumns": "minmax(0,0.85fr) minmax(0,1.35fr) minmax(0,0.85fr)",
  "_gridTemplateColumns:tablet_portrait": "var(--brxw-grid-1)",
  "_gridGap": "0",
  "_alignItemsGrid": "stretch",
  "_overflow": "hidden",
  "_background": {"color": {"raw": "var(--surface-white)"}},
  "_border": {
    "width": {"top": "1", "right": "1", "bottom": "1", "left": "1"},
    "style": "solid",
    "color": {"raw": "var(--brxw-color-neutral-trans-10)"},
    "radius": {
      "top": "var(--brxw-radius-m)",
      "right": "var(--brxw-radius-m)",
      "bottom": "var(--brxw-radius-m)",
      "left": "var(--brxw-radius-m)"
    }
  }
}
```

### Card/panel

```json
{
  "_padding": {
    "top": "var(--brxw-space-xl)",
    "right": "var(--brxw-space-xl)",
    "bottom": "var(--brxw-space-xl)",
    "left": "var(--brxw-space-xl)"
  },
  "_rowGap": "var(--brxw-space-s)",
  "_background": {"color": {"raw": "var(--surface-soft)"}},
  "_border": {
    "radius": {
      "top": "var(--brxw-radius-m)",
      "right": "var(--brxw-radius-m)",
      "bottom": "var(--brxw-radius-m)",
      "left": "var(--brxw-radius-m)"
    }
  }
}
```

### Repeated card structure rule

For repeated card/grid sections, use real card containers rather than loose text elements directly inside a grid.

Good:

```text
Grid parent
  Card block/div
    Icon wrapper
      Icon or SVG
    Heading
    Text
```

Avoid:

```text
Grid parent
  Text item
  Text item
  Text item
```

### Hero with background image and overlay

Use the section background image for heroes.

```json
{
  "id": "hero01",
  "name": "section",
  "parent": 0,
  "children": ["ovr001", "con001"],
  "settings": {
    "_aspectRatio": "16/9",
    "_position": "relative",
    "_overflow": "hidden",
    "_isolation": "isolate",
    "_background": {
      "image": {
        "id": 123,
        "filename": "hero-image.jpg",
        "size": "large",
        "full": "https://example.com/wp-content/uploads/hero-image.jpg",
        "url": "https://example.com/wp-content/uploads/hero-image-1024x576.jpg"
      },
      "position": "center center",
      "size": "cover",
      "repeat": "no-repeat"
    }
  },
  "label": "Hero"
}
```

Overlay child:

```json
{
  "id": "ovr001",
  "name": "div",
  "parent": "hero01",
  "children": [],
  "settings": {
    "_position": "absolute",
    "_top": "0",
    "_right": "0",
    "_bottom": "0",
    "_left": "0",
    "_zIndex": "-1",
    "_background": {"color": {"raw": "var(--brxw-color-overlay)"}}
  },
  "label": "Overlay"
}
```

For overlays, the section should have `_position: relative`, `_isolation: isolate`, and `_overflow: hidden`.

---

## 8. Responsive Rules

Use Bricks breakpoint suffixes:

```text
_gridTemplateColumns:tablet_portrait
_gridTemplateColumns:mobile_landscape
_gridTemplateColumns:mobile_portrait
_padding:mobile_landscape
_width:tablet_portrait
```

Common pattern:

```json
{
  "_gridTemplateColumns": "var(--brxw-grid-3)",
  "_gridTemplateColumns:tablet_portrait": "var(--brxw-grid-1)",
  "_gridTemplateColumns:mobile_landscape": "var(--brxw-grid-1)"
}
```

When using CSS code elements, normal media queries are also fine:

```css
@media (max-width: 991px) {}
@media (max-width: 767px) {}
```

Mobile default preference:

- Use a maximum of 2 columns for card/icon grids on narrow mobile screens.
- Centre stacked cards, icons and content unless there is a clear reason not to.
- Avoid horizontal overflow.
- Use `minmax(0, 1fr)` for grid columns that might overflow.

---

## 9. Code Elements and JS/CSS

### Code element patterns

CSS code element:

```json
{
  "id": "css001",
  "name": "code",
  "parent": "sec001",
  "children": [],
  "settings": {
    "executeCode": true,
    "noRoot": true,
    "cssCode": ".section-class .target{...}"
  },
  "label": "Section CSS"
}
```

JS code element:

```json
{
  "id": "js0001",
  "name": "code",
  "parent": "sec001",
  "children": [],
  "settings": {
    "executeCode": true,
    "noRoot": true,
    "javascriptCode": "document.addEventListener('DOMContentLoaded',function(){...});"
  },
  "label": "Section JS"
}
```

### Placement

For interactive sections:

```text
section
  container with normal Bricks elements
  CSS code element
  JS code element
```

The section `children` array must include all three children.

### Code block script rule

When supplying JavaScript for a Bricks/BricksForge code element, do **not** include `<script>` or `</script>` tags unless the user specifically asks for raw HTML embed code.

Good:

```js
(function () {
  // code here
})();
```

Avoid:

```html
<script>
(function () {
  // code here
})();
</script>
```

### Scoped JS pattern

Use section-scoped JS so multiple copies can exist safely:

```js
document.addEventListener('DOMContentLoaded', function () {
  document.querySelectorAll('.unique-section-class').forEach(function(section) {
    const data = {};
    const title = section.querySelector('.title-class');

    function setActive(key) {
      const item = data[key];
      if (!item) return;
      if (title) title.textContent = item.title;

      section.querySelectorAll('[data-item]').forEach(function(el) {
        el.classList.toggle('is-active', el.getAttribute('data-item') === key);
      });
    }

    section.querySelectorAll('[data-item]').forEach(function(el) {
      el.addEventListener('click', function(event) {
        event.preventDefault();
        setActive(el.getAttribute('data-item'));
      });
    });
  });
});
```

### Interactive button link warning

When JS updates a Bricks button, setting `button.textContent` works visually, but Bricks button link structure may vary. If link updates are unreliable, use a real `<a>` inside a `text` element or use JS click handling.

If a JS data object controls button links, update the JS object as well as any static button settings.

Example:

```js
buttonText: 'View product',
buttonUrl: '/product-page/'
```

---

## 10. Interactive Image and Hotspot Patterns

### Hotspot explorer

Proven structure:

- Native Bricks elements.
- Fixed-ratio image stage.
- Percentage-positioned hotspot buttons with data attributes.
- Separate detail card updated by JS.
- Product chips if needed.
- CSS/JS code elements inside the section.

Safer layout:

```text
section
  container
    two-column grid
      left: fixed-ratio image with hotspots
      right: separate detail card
    product chips below
  CSS code element
  JS code element
```

Avoid putting a large overlay card over the image if it hides hotspots.

### Data attributes

```json
{
  "_attributes": [
    {"id": "at1001", "name": "data-hotspot", "value": "boxes"},
    {"id": "at1002", "name": "aria-label", "value": "Show postal boxes"}
  ]
}
```

### Failed image-swap methods to avoid

Do not use:

1. Hidden image bank method: hidden Bricks images in a `display:none` bank and JS reading URLs.
2. Stacked absolute image method: opacity/visibility toggling between stacked Bricks images.

Both caused blank or unreliable images.

### Proven image gallery pattern: conveyor track

Use a horizontal conveyor/track:

```text
gallery display frame
  gallery track
    Bricks image 1
    Bricks image 2
    Bricks image 3
selector buttons/cards with data-slide-index
```

CSS:

```css
.gallery__display {
  aspect-ratio: 16 / 10;
  position: relative;
  overflow: hidden;
}

.gallery__track {
  display: flex !important;
  flex-direction: row !important;
  flex-wrap: nowrap !important;
  width: 100% !important;
  height: 100% !important;
  transition: transform .45s ease;
  will-change: transform;
}

.gallery__track > * {
  flex: 0 0 100% !important;
  width: 100% !important;
  min-width: 100% !important;
  max-width: 100% !important;
  height: 100% !important;
  margin: 0 !important;
  position: relative !important;
  display: block !important;
}

.gallery__track > * figure,
.gallery__track > * picture,
.gallery__track > * img {
  display: block !important;
  width: 100% !important;
  height: 100% !important;
  max-width: none !important;
}

.gallery__track > * img {
  object-fit: cover !important;
  object-position: center !important;
}
```

JS:

```js
function updateGallery(key, index) {
  const item = data[key];
  if (!item) return;

  if (track) {
    track.style.transform = 'translateX(-' + (index * 100) + '%)';
  }

  if (title) title.textContent = item.title;
  if (description) description.textContent = item.description;

  section.querySelectorAll('[data-item]').forEach(function(el) {
    el.classList.toggle('is-active', el.getAttribute('data-item') === key);
  });
}
```

Selector attributes:

```json
{
  "_attributes": [
    {"id": "at2001", "name": "data-item", "value": "item-two"},
    {"id": "at2002", "name": "data-slide-index", "value": "1"}
  ]
}
```

Translation values:

```text
slide 0 = translateX(0%)
slide 1 = translateX(-100%)
slide 2 = translateX(-200%)
slide 3 = translateX(-300%)
```

Do not use `width: 300%` plus slide width `33.333%`; this showed all images at once. Use track width `100%`, each child `100%`, and translate by `index * 100`.

### Mobile interactive image pattern

For complex desktop product builders:

- Desktop/tablet: show the full product lineup with markers/overlay if useful.
- Mobile: hide the complex lineup and show a simplified single-product preview image.
- Keep selected size/spec reflected in chips, summary text and title.
- Use separate desktop and mobile conveyor tracks if needed.

Example classes:

```text
.builder__track--desktop
.builder__track--mobile
```

Both tracks can use the same conveyor method. JS should update both tracks from the same state.

---

## 11. Native Forms

Native Bricks `form` elements can be generated successfully.

Use native Bricks forms for simple launch/contact forms unless the user specifically needs BricksForge Pro Forms.

Important:

- Use short field IDs, preferably 6 characters.
- Use clear labels and placeholders.
- Start with `actions: ["email"]` for simple launch forms.
- BricksForge or webhook integrations can be added separately later.

Basic form:

```json
{
  "id": "frm001",
  "name": "form",
  "parent": "pa1001",
  "children": [],
  "settings": {
    "_cssClasses": "site-contact-form",
    "fields": [
      {"type": "text", "label": "Full name", "placeholder": "Your full name", "required": true, "id": "fname1"},
      {"type": "email", "label": "Email", "placeholder": "name@example.com", "required": true, "id": "email1"},
      {"type": "textarea", "label": "Message", "placeholder": "How can we help?", "required": true, "id": "msg001"}
    ],
    "submitButtonStyle": "primary",
    "actions": ["email"],
    "successMessage": "Thanks for your enquiry. We will get back to you as soon as possible.",
    "emailSubject": "Website enquiry",
    "emailTo": "admin_email",
    "fromName": "Website",
    "emailErrorMessage": "Submission failed. Please reload the page and try again.",
    "htmlEmail": true,
    "showLabels": true
  },
  "label": "Contact Form"
}
```

---

## 12. BricksForge Pro Forms

### General rules

BricksForge Pro Forms can be represented in clipboard JSON as `brf-pro-forms` with children such as:

- `brf-pro-forms-field-step`
- `brf-pro-forms-field-next`
- `brf-pro-forms-field-previous`
- `brf-pro-forms-field-submit-button`
- `brf-pro-forms-field-radio-wrapper`
- `brf-pro-forms-field-radio`
- `brf-pro-forms-field-checkbox-wrapper`
- `brf-pro-forms-field-checkbox`
- `brf-pro-forms-field-calculation`
- `brf-pro-forms-field-live-value`
- `brf-pro-forms-field-file-download`

For Pro Forms work:

- Preserve existing element IDs where possible.
- Preserve `id` values inside field settings.
- Field setting IDs are used for submitted field names, PDF merge tags, webhook mappings and JS selectors.
- Preserve `data-custom-id` assumptions if JS depends on them.
- Treat Pro Forms field IDs as functional data keys, not visual identifiers.

### Pro Forms PDF templates

BricksForge PDF generation can use an HTML template stored in:

```text
createPdfHtmlTemplate
```

Useful settings observed:

```text
actions includes "create_pdf" and "show_download_info"
createPdfTemplateType: "html"
createPdfPaperSize: "A4"
createPdfOrientation: "portrait"
createPdfShowDownloadAfterSubmission: true
createPdfAddAsAttachment: true
createPdfName: supports merge tags such as configuration_{{name}} or enquiry_{{email}}
```

PDF HTML should use email-like markup:

- Tables for layout.
- Inline styles.
- Absolute image URLs.
- Simple `img` tags.
- Merge tags for field values, e.g. `{{pdf_hob_type}}`.
- Avoid scripts, external fonts and complex CSS.

PDF generators may not support `object-fit` reliably. To avoid squashed images, use square/fixed image areas and avoid forcing mismatched aspect ratios.

For more predictable PDF margins:

- Use `body` margin/padding `0`.
- Reduce outer wrapper padding.
- Use `width:100%` or a wider outer container.
- Accept that the PDF engine may still apply print/page margins unless plugin settings expose margin control.

### Hidden field plumbing

For complex configurators, add hidden fields to store clean, PDF/webhook-ready values.

Reason:

Visible radio/checkbox fields work for the user interface, but PDF templates and webhook mappings often need stable, simple values. JS can read selected visible fields and write cleaned values into hidden fields.

Useful hidden field examples:

```text
selected_items
pdf_hob_type
pdf_toilet_type
pdf_water_storage
pdf_furniture_colour
pdf_upholstery_fabric
pdf_flooring_colour
pdf_end_wall_colour
pdf_plexiglass_colour
pdf_alloy_wheel_colour
hob_type_icon
toilet_type_icon
water_storage_icon
furniture_colour_image
upholstery_fabric_image
flooring_colour_image
end_wall_colour_image
plexiglass_colour_image
alloy_wheel_colour_image
selected_extras_html
```

Pattern:

- Visible fields are for UX.
- Hidden fields are for PDFs, webhook data and email content.
- JS updates hidden fields on load, click, change and before submit.

Useful Pro Forms field lookup:

```js
function getField(form, id) {
  return (
    form.querySelector('[name="form-field-' + id + '"]') ||
    form.querySelector('#form-field-' + id) ||
    form.querySelector('[data-custom-id="' + id + '"] input, [data-custom-id="' + id + '"] textarea')
  );
}
```

### Clickable card radio/checkbox pattern

For card-style Pro Forms choices, the actual label/radio wrapper may be the only clickable area by default. Use JS to make the whole card clickable.

Recommended structure:

- Each option card gets a shared class such as `cfg-card-radio`.
- The card contains the visual image/icon and the Pro Forms radio/checkbox wrapper.
- JS listens for clicks on `.cfg-card-radio`, finds the input inside, checks it, and dispatches `input` and `change`.

Important:

- Do not trigger when clicking links/buttons inside the card.
- Dispatch both `input` and `change` so calculations, live values, summary output and custom JS update correctly.
- Keep the JS scoped to the form/section.

### Configurator card sizing rule

For choice-card layouts, especially swatches, prefer fixing card sizing with Bricks element settings rather than relying on custom CSS.

Good pattern:

- Set a sensible fixed width or max-width on the card.
- Set a consistent `_minHeight`.
- Use grid parent columns that allow consistent card sizes across rows.
- Set the swatch wrapper to a fixed height/width or consistent aspect ratio.
- Use `_objectFit: cover` for texture/swatch images.
- Use `_objectFit: contain` for icons/wheels.

For multi-row configurators, establish one shared card size based on the row with the most options. If the fabric row has 11 cards, choose a card size that works there first, then reuse it for furniture, flooring, wall, plexiglass and wheel rows.

### Multiple code blocks on one form

It is fine to use separate code blocks for different responsibilities:

1. Configurator review/PDF hidden-field updater.
2. UTM attribution hidden-field updater.

To reduce conflicts:

- Scope configurator JS to the form ID/class.
- Do not use global click handlers unless needed.
- Make UTM JS defensive and lightweight.
- Avoid repeated expensive DOM queries on every click if the form is multi-step.
- Wrap update logic in `try/catch` so one failure does not freeze the form.
- Avoid `preventDefault()` unless absolutely necessary.
- Do not include `<script>` tags inside Bricks code elements.

### Lead-gen configurator download pattern

For configurator lead generation:

- Put configuration/review steps before contact details.
- Final step collects simple contact details.
- On submit, generate the PDF and show the download button.
- The download button is the incentive for submitting.
- Avoid a separate “PDF step” unless needed.
- A `brf-pro-forms-field-file-download` element can sit inside the final form card.
- Use `createPdfShowDownloadAfterSubmission: true` and `show_download_info` where appropriate.

UX rule:

Keep the final contact step simple: name, email, phone, optional message, consent, submit/download button. The review step is where users check selections.

---

## 13. Webhook and Attribution Patterns

### BricksForge webhook settings

For external form intake, add `"webhook"` to the Pro Forms `actions` array and configure `pro_forms_post_action_webhooks`.

Known working shape:

```json
{
  "url": "https://example.com/api/forms/submit",
  "method": "POST",
  "content_type": "json",
  "data": [
    {"key": "site", "value": "example.com"},
    {"key": "siteSection", "value": "main-site"},
    {"key": "formKey", "value": "contact-form"},
    {"key": "formName", "value": "Contact Form"},
    {"key": "pageUrl", "value": "{post_url}"},
    {"key": "contact.fullName", "value": "name"},
    {"key": "contact.email", "value": "email"},
    {"key": "contact.phone", "value": "phone"},
    {"key": "message", "value": "message"},
    {"key": "consents.marketing", "value": "marketing_consent"}
  ],
  "headers": [
    {"key": "x-webhook-key", "value": "WEBHOOK_KEY_HERE"}
  ]
}
```

Do not include real webhook keys in README files or generated public docs.

Important: in BricksForge webhook data mapping, values often reference the form field ID directly, such as `name`, `email`, `phone`, rather than `{{name}}`. Check against a known working form on the same site/plugin version.

### UTM hidden fields

For attributed BricksForge forms, use hidden fields for:

```text
utm_source
utm_medium
utm_campaign
utm_content
utm_term
utm_id
first_utm_source
first_utm_medium
first_utm_campaign
first_utm_content
first_utm_term
first_utm_id
landing_page_url
submission_page_url
referrer_url
```

A separate page-level JS block can:

- Read UTM params from the current URL.
- Store first-touch and last-touch attribution in `localStorage`.
- Populate hidden fields on page load and shortly after.
- Populate again on submit/click if needed.

Expected behaviour:

If a visitor arrives with UTM parameters, then later returns directly without UTM parameters, stored attribution may still populate the hidden fields. This is intentional for attribution continuity.

Testing note:

Use an incognito/private window or clear the site’s `localStorage` when testing “no UTM” submissions. Otherwise old stored UTM values can appear in the destination system and look like a bug.

---

## 14. Header, Offcanvas and Mega Menu Patterns

### Native header basics

Useful structure:

```text
section
  container
    header inner
      left area
      logo area
      right actions
```

Common header elements:

- `svg` for a placeholder/imported logo SVG.
- `logo` if using the WordPress/Bricks site logo.
- `search` for native Bricks search overlay.
- `button` for CTA links.
- `toggle` only for native Bricks offcanvas/mobile menu triggers.
- normal `button` for custom desktop menu triggers.

### Header grid

```json
{
  "_display": "grid",
  "_gridTemplateColumns": "minmax(0,1fr) auto minmax(0,1fr)",
  "_gridGap": "var(--brxw-grid-gap)",
  "_alignItemsGrid": "center"
}
```

Use `minmax(0,1fr)`, not plain `1fr`, to avoid horizontal overflow.

### Native search

```json
{
  "id": "srch01",
  "name": "search",
  "parent": "act001",
  "children": [],
  "settings": {
    "searchOverlayTitle": "Search site",
    "searchType": "overlay",
    "buttonAriaLabel": "Search",
    "_cssClasses": "site-header__search",
    "icon": {
      "library": "fontawesomeSolid",
      "icon": "fas fa-magnifying-glass"
    },
    "iconHeight": "22",
    "iconWidth": "22"
  },
  "label": "Search"
}
```

### Native offcanvas structure

Exported structure:

```text
offcanvas
  block.brx-offcanvas-inner
    content
    close toggle
  block.brx-offcanvas-backdrop
```

Offcanvas open toggle:

```json
{
  "id": "mtog01",
  "name": "toggle",
  "parent": "act001",
  "children": [],
  "settings": {
    "toggleSelector": "#brxe-off001",
    "ariaLabel": "Open mobile menu",
    "_cssClasses": "site-header__mobile-toggle"
  },
  "label": "Mobile Offcanvas Toggle"
}
```

After pasting, verify the actual offcanvas ID. If Bricks changes the ID to `ogaiau`, update the toggle selector to `#brxe-ogaiau`.

### Plugin-free mega menu pattern

Use separate desktop and mobile systems.

Desktop:

- Use a normal Bricks `button`, not `toggle`.
- Add `data-mega-toggle="true"`.
- Add `aria-expanded="false"`.
- Use scoped JS to add/remove `.is-open` on the desktop mega panel.

Mobile:

- Use native Bricks `toggle`.
- Wire it to the offcanvas using `toggleSelector`.

Desktop trigger example:

```json
{
  "id": "mgb001",
  "name": "button",
  "parent": "hleft1",
  "children": [],
  "settings": {
    "text": "Menu",
    "style": "secondary",
    "outline": true,
    "_cssClasses": "site-header__desktop-menu-button",
    "_attributes": [
      {"id": "at01aa", "name": "data-mega-toggle", "value": "true"},
      {"id": "at02aa", "name": "aria-expanded", "value": "false"},
      {"id": "at03aa", "name": "aria-label", "value": "Open menu"}
    ]
  },
  "label": "Desktop Mega Menu Button"
}
```

Mega panel example:

```json
{
  "id": "mega01",
  "name": "block",
  "parent": "head01",
  "children": ["mgcon1"],
  "settings": {
    "_cssClasses": "site-mega-panel",
    "_position": "absolute",
    "_top": "100%",
    "_left": "0",
    "_right": "0",
    "_zIndex": "998"
  },
  "label": "Desktop Mega Panel"
}
```

Important:

- Desktop mega trigger must not have `toggleSelector`.
- Mobile Bricks toggle must have `toggleSelector`.
- Hide desktop trigger on mobile/tablet.
- Hide mobile trigger on desktop.

### Mega menu visual structure

```text
mega panel inner
  intro panel
    eyebrow
    headline
    short text
    button
  product/category columns
    column title
    links
  promo panel
    image
    heading
    text
    button
```

### Header overflow fixes

If the page becomes horizontally scrollable:

```css
html,
body {
  width: 100%;
  max-width: 100%;
  overflow-x: hidden !important;
}

.site-header,
.site-header *,
.site-offcanvas,
.site-offcanvas * {
  box-sizing: border-box;
}

.site-header {
  width: 100% !important;
  max-width: 100% !important;
  min-width: 0 !important;
  overflow-x: clip;
}

.site-header__inner {
  grid-template-columns: minmax(0,1fr) auto minmax(0,1fr) !important;
  min-width: 0 !important;
  width: 100% !important;
  max-width: 100% !important;
}
```

Also set `min-width: 0` on grid/flex children and move mega-panel horizontal padding to the child container, not the absolute panel.

Troubleshooting:

- Desktop menu opens mobile offcanvas: desktop trigger is probably a Bricks `toggle`; replace with `button`.
- Mobile offcanvas does not open: check actual offcanvas ID and update `toggleSelector`.
- Horizontal scroll: check closed header grid first, not only open mega menu.

---

## 15. Footer Pattern

For complex footer grids:

- Use strict 6-character IDs.
- Avoid long child IDs.
- Use separate child elements for structured mini content.
- Prefer FontAwesome `icon` elements for small trust/contact/CTA icons.
- Keep logo as replaceable `svg` where needed.
- Avoid inline `<br><span>` inside `text-basic`.

Footer visual structure:

```text
footer section
  container
    main grid
      brand column
      solutions links
      products links
      company links
      contact column
    CTA strip
    bottom legal bar
  CSS code element
```

Trust/contact/CTA item structure:

```text
Trust item
  icon
  block
    heading
    text-basic

Contact item
  icon
  block
    heading
    text-basic

CTA strip
  icon
  block
    heading
    text-basic
  button
```

Full-width footer bottom bar fix:

```css
.footer__bottom {
  width: 100vw;
  max-width: 100vw;
  position: relative;
  left: 50%;
  transform: translateX(-50%);
}
```

Long contact text wrapping:

```css
.footer__mini-heading,
.footer__mini-text {
  overflow-wrap: anywhere;
}
```

---

## 16. Contact Page Pattern

Useful structure:

```text
contact page section
  container
    hero
      left copy
      right image
    main grid
      left form card
      right side column
        contact details card
        what happens next card
    what to include cards
    bottom CTA
```

Native Bricks form guidance:

- Works as pasteable JSON.
- Use short field IDs.
- Use native email action for simple launch forms.
- CSS can style native fields using section-scoped selectors.

Example content structure:

Hero:

- Get a quote.
- Tell us what you need and we will help you find the right option for your project.

Form card fields:

- Full name
- Business email
- Company name
- Phone number
- Industry / sector
- Product or service interested in
- Requirements
- Estimated quantity
- Timeline
- Optional file upload
- Privacy consent

Side card:

- Email
- Phone
- Business hours
- Service area

What happens next:

1. We review your enquiry.
2. We suggest the right options.
3. You receive your quote.

What to include:

- Product or service type
- Approx quantity or scope
- Branding/customisation needs
- Delivery deadline
- Supporting files

---

## 17. Proven Section Types

These section types have worked as pasteable Bricks JSON:

1. Hero + intro section.
2. Vanilla product/industry section.
3. Vanilla split-card section.
4. Hotspot explorer.
5. Safer two-column hotspot explorer with separate detail card.
6. Interactive image gallery using conveyor/track method.
7. Header with plugin-free desktop mega menu + native mobile offcanvas.
8. Multi-column footer with strict 6-character IDs.
9. Contact / enquiry page with native Bricks form.
10. BricksForge multi-step configurator rows and hidden field blocks.

---

## 18. Troubleshooting

### Empty nested blocks after paste

Symptoms:

- Parent blocks appear.
- Child elements are missing.
- No obvious import error.

Most likely cause:

- IDs longer than 6 characters.

Fix:

- Regenerate with exactly 6-character IDs.
- Re-check every `children` array.
- Keep readable meaning in `label`, not `id`.

### Images go blank in interactive galleries

Avoid:

- Hidden image banks.
- Opacity-swapped stacked images.

Use:

- Conveyor/track pattern with real Bricks image elements side-by-side.

### Form hangs or multi-step navigation breaks

Likely causes:

- JS preventing default clicks.
- Broad/global click handlers.
- JS errors during form step transitions.
- UTM or hidden-field script doing too much on every click.

Fix:

- Scope JS to the specific form/section.
- Wrap update functions in `try/catch`.
- Avoid `preventDefault()` unless required.
- Use defensive selectors.
- Keep separate code blocks lightweight.

### UTM values appear without URL parameters

Likely cause:

- Attribution was previously stored in `localStorage`.

Fix for testing:

- Use incognito/private window.
- Clear localStorage for the site.

### Mobile offcanvas does not open

Check:

- Actual offcanvas element ID after paste.
- Mobile toggle `toggleSelector`.
- Selector format must be `#brxe-{actualOffcanvasId}`.

### Desktop mega menu opens mobile offcanvas

Cause:

- Desktop trigger is probably a Bricks `toggle` element.

Fix:

- Use a normal `button` for desktop menu trigger.
- Keep Bricks `toggle` only for mobile/offcanvas.

### Horizontal page scroll after header paste

Check:

- Closed header grid, not only open mega menu.
- Use `minmax(0,1fr)` grid columns.
- Add `min-width: 0` to header children.
- Clamp containers to `max-width: 100%`.
- Add `overflow-x: hidden` on `html, body` as a safety net.

---

## 19. Do / Do Not

### Do

- Output Bricks clipboard JSON, not HTML/CSS paste workflow.
- Use native Bricks elements.
- Use strict 6-character IDs.
- Use readable `label` values.
- Use direct settings first.
- Use native Bricks controls instead of CSS blocks wherever practical.
- Use BEM classes for scoped hooks.
- Use global classes where repeated native editing is useful.
- Include icons if the mockup includes icons.
- Use wrapper divs for SVG placeholders.
- Use real card containers for repeated cards.
- Scope all CSS/JS.
- Use conveyor-track pattern for image galleries.
- Replace large BricksForge forms in small, safe chunks.

### Do not

- Do not output HTML/CSS only unless specifically requested.
- Do not use Bricks’ HTML/CSS paste workflow.
- Do not depend on unconfirmed Brixies/ACSS variables such as `--base`.
- Do not use remote Brixies placeholder images.
- Do not include invalid parent/child relationships.
- Do not omit IDs from parent `children` arrays.
- Do not use IDs longer than 6 characters.
- Do not use descriptive IDs like `mobileToggle` or `megaPanel`.
- Do not use unscoped CSS/JS.
- Do not add CSS code blocks for layout or styling that can be handled by native Bricks controls.
- Do not repeat ID-specific CSS for repeated cards when a shared class would work.
- Do not overuse global classes for one-off items.
- Do not use hidden image banks or opacity-swapped stacked images.
- Do not put structured mini content into one `text-basic` element using `<br><span>`.
- Do not use empty SVG placeholders for lots of repeated small icons unless specifically requested.
- Do not include real webhook keys in README docs or public examples.

---

## 20. Final Pre-Flight Checklist for Generated JSON

Before sending a blob, check:

```text
[ ] Clipboard wrapper is valid.
[ ] All IDs are exactly 6 characters.
[ ] All IDs are unique.
[ ] All children exist.
[ ] All parents exist.
[ ] Top-level section has parent 0.
[ ] Code elements are included in parent children arrays.
[ ] CSS/JS is scoped.
[ ] CSS code blocks are only used where native Bricks settings are not enough.
[ ] Repeated hover/behaviour styling uses shared classes where appropriate.
[ ] JS has no <script> tags for Bricks code blocks.
[ ] Responsive settings are included where needed.
[ ] Repeated cards use card containers.
[ ] Icons/SVG placeholders are included where expected.
[ ] Image URLs are appropriate for the site.
[ ] Links are static or JS data objects are updated too.
[ ] For BricksForge forms, field IDs and functional IDs are preserved.
[ ] For large forms, only the requested row/step/block is replaced.
```
