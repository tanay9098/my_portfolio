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
- There is no top navbar and no footer. The only fixed control is the theme toggle (top-right).

## 2. Colour

Two themes: **dark (ink)** and **light (paper)**. The neutrals match princepal.dev; the accent
comes from the "Cyber Lime & Ink + Violet" palette (`#C6F36B`). Text is neutral black, white or
grey. The accent (lime in dark, violet in light) is used only for tech pills, text selection
and the cursor glow.

The theme follows the visitor's system setting on first visit. A small sun/moon button fixed in
the top-right corner switches it, and the choice is saved in `localStorage` (`theme`). A tiny
inline script in `<head>` sets `data-theme` on `<html>` before first paint, so there is no flash.
Dark tokens live on `:root`; light tokens override them on `:root[data-theme="light"]`.

| Token              | Dark                       | Light                      | Use                                     |
|--------------------|----------------------------|----------------------------|-----------------------------------------|
| `--bg`             | `#0b0b0b`                  | `#ffffff`                  | Page background                         |
| `--bg-blur`        | `rgba(11,11,11,.75)`       | `rgba(255,255,255,.75)`    | Sticky section heading (mobile)         |
| `--surface`        | `#202020`                  | `#f5f5f5`                  | Avatar circle                           |
| `--surface-hover`  | `#141414`                  | `#f5f5f5`                  | Card or row background on hover         |
| `--heading`        | `#e8e8e8`                  | `#0a0a0a`                  | Name, headings, titles, bold body text  |
| `--white`          | `#ffffff`                  | `#000000`                  | Hovered or active headings and links    |
| `--text`           | `#a3a3a3`                  | `#525252`                  | Body copy                               |
| `--muted`          | `#858585`                  | `#737373`                  | Dates, labels, inactive nav, captions   |
| `--line`           | `rgba(232,232,232,.12)`    | `#e5e5e5`                  | Hairline borders, thumbnail frames      |
| `--line-hover`     | `rgba(232,232,232,.3)`     | `#d4d4d4`                  | Border on hover                         |
| `--accent`         | `#c6f36b` (cyber lime)     | `#6d28d9` (violet)         | Accent colour                           |
| `--pill-bg`        | `rgba(198,243,107,.1)`     | `rgba(124,58,237,.08)`     | Tech-pill background                    |
| `--pill-text`      | `#c6f36b`                  | `#6d28d9`                  | Tech-pill text                          |
| `--selection-bg`   | `#c6f36b`                  | `rgba(124,58,237,.2)`      | Text selection background               |
| `--selection-text` | `#0b0b0b`                  | `#0a0a0a`                  | Text selection colour                   |
| `--spotlight`      | `rgba(139,92,246,.1)`      | `rgba(124,58,237,.06)`     | Glow that follows the cursor            |

- **Tech pills** are the main coloured element. To make them monochrome, set
  `--pill-bg: var(--surface-hover)` and `--pill-text: var(--heading)` in both themes.
- **Spotlight:** a 600px radial gradient sits behind all content and follows the mouse. It only
  runs on devices with a fine pointer that hover. It lights the background and never colours text.
- `theme-color` meta is updated to match the active theme.

## 3. Typography

- **Content font:** Geist Mono (Google Fonts), weights 400, 500, 600, 700. Fallback: SF Mono,
  Monaco, Cascadia Code, system monospace. Used for everything except the section nav.
- **UI font:** Hanken Grotesk (Google Fonts), weights 400–700. Used for the section nav labels.
  Fallback: system sans-serif.
- **Base:** 16px, line-height 1.625, antialiased.

| Element              | Size / weight                     | Colour      |
|----------------------|-----------------------------------|-------------|
| Name (`h1`)          | 36px → 48px (sm+), 700, tight     | `--heading` |
| Title (`h2`)         | 18px → 20px, 500                  | `--heading` |
| Tagline              | 16px, 400, max 320px wide         | `--text`    |
| Nav label (sans)     | 12px, 700, uppercase, wide track  | muted → heading |
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

### Theme toggle
- 36px square button fixed at the top-right corner (12px inset), above all content. It does not
  take part in the page layout.
- Shows a moon in dark mode and a sun in light mode, 18px, `--text` turning `--heading` on hover
  with a `--surface-hover` background. Its `aria-label` names the theme it switches to.

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
