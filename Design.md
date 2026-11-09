# Design.md — tanaydwivedi.com

The design spec for the portfolio. `index.html` must follow this file; update this
file first whenever the design changes.

The site is one static page (`index.html`, inline CSS and JS, no build step) plus
images in `assets/`.

---

## 1. Layout: split screen

```
┌───────────────────────────── max 1280px ─────────────────────────────┐
│  LEFT  (sticky, 48%)               │  RIGHT  (scrolls, 52%)          │
│                                    │                                 │
│  Tanay Dwivedi            (name)   │  About                          │
│  Full-Stack Developer     (title)  │  Skills                         │
│  One-line tagline                  │  Experience                     │
│  Location · time-zone overlap      │  Projects                       │
│                                    │  Testimonials                   │
│  ──── ABOUT        (section nav)   │  GitHub                         │
│  ──── SKILLS                       │  Education                      │
│  ──── EXPERIENCE                   │  Contact                        │
│  ──── …                            │                                 │
│                                    │                                 │
│  [gh] [in] [x] [dev] [mail]        │                                 │
└────────────────────────────────────┴─────────────────────────────────┘
```

- **Desktop (≥ 1024px):** two columns. The left `<header>` is `position: sticky; top: 0;
  height: 100vh` and spreads its content from top to bottom. Only the right `<main>` scrolls.
- **Tablet/mobile (< 1024px):** one column. The header sits at the top and does not stick. The
  section nav is hidden. Each section's heading sticks to the top of the screen while you scroll
  through that section, on a blurred background.
- Page gutters: 24px on mobile, 48px from 768px, 96px from 1024px.
- Vertical spacing: sections are separated by 64 / 96 / 144px (mobile / tablet / desktop).
- There is no top navbar and no footer.

## 2. Colour

The palette is dark navy and slate. **Text is white or grey only.** No purple, violet or blue
text, gradients or glows on text.

| Token              | Value                      | Use                                            |
|--------------------|----------------------------|------------------------------------------------|
| `--bg`             | `#0f172a`                  | Page background (navy)                         |
| `--surface-hover`  | `rgba(30,41,59,.5)`        | Card or row background on hover                |
| `--heading`        | `#e2e8f0`                  | Name, headings, titles, bold text in body copy |
| `--white`          | `#ffffff`                  | Hovered or active headings and links           |
| `--text`           | `#94a3b8`                  | Body copy                                      |
| `--muted`          | `#64748b`                  | Dates, labels, inactive nav, captions          |
| `--line`           | `rgba(226,232,240,.1)`     | Hairline borders, thumbnail frames             |
| `--line-hover`     | `rgba(226,232,240,.3)`     | Border on hover                                |
| `--pill-bg`        | `rgba(45,212,191,.1)`      | Tech-pill background                           |
| `--pill-text`      | `#5eead4`                  | Tech-pill text                                 |
| `--spotlight`      | `rgba(29,78,216,.15)`      | Glow that follows the cursor (background only) |

- **Tech pills** are the only coloured element (soft teal). To make them monochrome too, set
  `--pill-bg: rgba(226,232,240,.08)` and `--pill-text: #e2e8f0`.
- **Spotlight:** a 600px radial gradient sits behind all content and follows the mouse. It only
  runs on devices with a fine pointer that hover. It lights the background and never colours text.
- **Selection:** teal-tinted background with white text.

## 3. Typography

- **Font:** Inter (Google Fonts), weights 400, 500, 600, 700. System sans-serif is the fallback.
- **Base:** 16px, line-height 1.625, antialiased.

| Element              | Size / weight                     | Colour      |
|----------------------|-----------------------------------|-------------|
| Name (`h1`)          | 36px → 48px (sm+), 700, tight     | `--heading` |
| Title (`h2`)         | 18px → 20px, 500                  | `--heading` |
| Tagline              | 16px, 400, max 320px wide         | `--text`    |
| Nav label            | 12px, 700, uppercase, wide track  | muted → heading |
| Section heading      | 14px, 700, uppercase, wide track  | `--heading` |
| Item title           | 16px, 500                         | `--heading` |
| Body / description   | 14–16px, 400                      | `--text`    |
| Date / meta column   | 12px, 600, uppercase, wide track  | `--muted`   |
| Pill                 | 12px, 500                         | `--pill-text` |

Bold text inside body copy uses `--heading` (500 weight) and is never coloured.

## 4. Components

### Section nav (left column, desktop only)
- Each item is a horizontal line followed by an uppercase label.
- Inactive: line is 32px wide in `--muted`, label is `--muted`.
- Hover or active: line grows to 64px and both turn `--heading`. Transition is 150ms.
- The active item follows the scroll position. The URL hash updates to match the active
  section, using `history.replaceState` so no history entries are added.

### Social icons (left column, bottom)
- 24px icons in `--text`, turning `--heading` on hover. Each has an `aria-label`.
- Links: GitHub, LinkedIn, X, dev.to, Email.

### List item (Experience, Projects, Education)
- Grid of 8 columns from 640px: a meta column (2 cols) and content (6 cols).
- Experience and Education put the date in the meta column. Projects put the thumbnail there.
- Hover (desktop): a backdrop extends 24px beyond the item with a `--surface-hover` background,
  a faint inset top highlight and a soft shadow. The title turns `--white`. The ↗ arrow nudges
  up and right.
- **Dim siblings (desktop):** when one item in a list is hovered, the others fade to 50% opacity.
- When the item has a URL, the whole card is clickable (the title link stretches over the card).

### Experience bullets
- Plain block-level `<li>` with a small `▹` marker positioned absolutely.
- **Never use `display: flex` on a `<li>` that contains inline `<strong>`.** Flex turns each
  text run into its own column, which was the bug in the old layout.

### Tech pills
- Rounded-full, 12px vertical / 4px horizontal padding, `--pill-bg` / `--pill-text`.
- Shown in a wrapping row with 8px gaps.

### Project thumbnail
- 16:10 frame, rounded 4px, 2px `--line` border that becomes `--line-hover` on card hover.
- `object-fit: cover`; `object-position` set per image.
- On mobile the thumbnail goes below the text and is at most 200px wide.

### Stats row (About, GitHub)
- Inline numbers with no boxes. The value is 24px / 700 / `--heading`; the label is 12px `--muted`.
- 2 columns on mobile, 4 columns from 640px.

### Testimonial
- Quote text in `--heading` with a large faint quotation mark.
- Author row: an initials avatar (slate circle, `--heading` text), name, and role and date
  in `--muted`.

### Text link with arrow
- `--heading`, 500 weight. The arrow moves 4px on hover; the text turns `--white`.

## 5. Motion and accessibility

- Every transition is 150–300ms.
- `prefers-reduced-motion: reduce` turns off smooth scrolling, the spotlight and transforms.
- A "Skip to content" link is visible when focused.
- Every interactive element has a visible `:focus-visible` outline (2px, `--heading`, offset).
- Images have descriptive `alt` text; decorative SVGs have `aria-hidden="true"`.
- Body text contrast against `--bg` is at least 4.5:1.

## 6. Content rules

- All copy is Tanay's own: about, roles, projects, testimonial, education and contact.
- Content stays in plain HTML in `index.html` so it can be edited without tooling.
