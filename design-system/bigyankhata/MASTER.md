# Design System Master File

> **LOGIC:** When building a specific page, first check `design-system/bigyankhata/pages/[page-name].md`.
> If that file exists, its rules **override** this Master file.
> If not, strictly follow the rules below.

---

**Project:** বিজ্ঞান খাতা · The Science Notebook (BigyanKhata)
**Category:** Bilingual (English / Bangla) science reference for general curious readers
**Style:** Editorial journal: printed-magazine hierarchy, pen-and-ink sketches on graph-paper plates, one spot colour
**Source of truth:** the `:root` tokens in `index.html`. Update both together.

This file records the final design of PR #3. The site was audited against UI UX Pro Max (`references/quick-reference.md`, §1–§9). The skill's generated suggestion (Swiss minimal, Exo + Roboto Mono) was **not** adopted. Those fonts have no Bengali glyphs, and the owner chose the editorial direction.

---

## Global Rules

### Color Palette

| Role | Hex | CSS Variable | Notes |
|------|-----|--------------|-------|
| Background | `#F3EEE3` | `--paper` | Warm paper; the only page background |
| Surface (hover / pressed) | `#EAE3D4` | `--paper-2` | Same hue family as the background |
| Card / plate | `#FFFFFF` | `--plate` | Sketch plates, index cards, inputs. Some sketches use `#fff` fills, so plates stay white |
| Foreground | `#1D1B16` | `--ink` | 14.9:1 on paper |
| Secondary text | `#4A453B` | `--ink-2` | 8.2:1 on paper |
| Muted text / labels | `#6B6457` | `--ink-3` | 5.1:1 on paper; the lowest allowed for text |
| Border | `#D8CFBD` | `--rule` | Hairlines between items |
| Plate grid | `#F1ECE2` | `--grid` | Graph-paper lines and index-card rules |
| Accent / CTA | `#A5401F` | `--accent` | Vermilion; 5.4:1 on paper. The only accent colour |
| On accent | `#FFFFFF` | `--on-accent` | 6.3:1 on accent |
| Accent (decorative) | `#C65A36` | `--accent-soft` | Sketch highlight fills and the index-card margin only; never text |

**Rules**
- One accent. Don't add a second hue for subjects or levels. Use weight, rules, dots and text instead (`color-not-only`).
- Tint shadows warm (`--lift`), never neutral black.
- Components use tokens only. No raw hex outside `:root` (`color-semantic`).
- Structural lines use `--ink` at 1px. Hairlines between list items use `--rule`.

### Typography

- **Display / headings:** Fraunces (variable, opsz 9–144, wght 300–700). Bangla falls back per glyph to **Tiro Bangla**.
- **Body / UI:** Hind Siliguri 400/500/600 (covers Latin and Bengali).
- **Labels / numbers:** IBM Plex Mono 400/500/600. It has **no Bengali glyphs**: under `html[lang="bn"]`, every mono label switches to `--sans` with no tracking and no uppercase.
- **Long-form article body:** Fraunces at opsz 18, 20px / 1.7, max 36em (≈70 characters).
- Fonts load from Google Fonts with `display=swap`.

**Type scale** (`font-scale`: every size comes from here; display sizes use `clamp()`)

| Token | Size | Use |
|-------|------|-----|
| `--fs-label` | 12px | Mono labels, meta lines, counts. Nothing smaller |
| `--fs-sm` | 14px | Buttons, secondary UI |
| `--fs-base` | 16px | Body, summaries, legends, inputs and selects (16px stops iOS zooming on focus) |
| `--fs-md` | 18px | Lede and intro paragraphs |
| `--fs-lg` | 20px | — |
| `--fs-xl` | 24px | Card titles, prev/next titles |
| `--fs-2xl` | 30px | Daily entry title, sub-headings |
| `--fs-3xl` | 40px | Stats numerals |
| display | `clamp(40px, 7vw, 98px)` | Page h1, article h2. Weight ~340, tracking −0.035em, line-height ≤1.0 (1.18–1.2 for Bangla) |

- Headings use `text-wrap: balance`. Paragraphs use `text-wrap: pretty`.
- Numbers use `tabular-nums` / lining figures. Bangla digits come from `nm()`; legend numbers stay Latin so they match the sketch markers.

### Spacing, layout & breakpoints

- 4/8px rhythm. Section spacing 44–110px; generous whitespace suits a reading site (density low).
- Container: `max-width: 1240px`. Gutters are 28px (desktop), 20px (≤768px) and 16px (≤640px).
- Breakpoints: **1024 / 768 / 640**. No horizontal scroll at 375px or in landscape.
- The entry grid has 12 columns. Items span 4. Positions 0 and 6 in every run of 10 span 8, which gives the zig-zag feature rows. Below 1024px: 6 columns (3 / 6). Below 640px: 1 column.
- `html { scroll-padding-top }` covers the sticky header (66px + 24px; 128px on phones), and the article overlay has its own (`focus-not-obscured`).

### Motion

| Token | Value | Use |
|-------|-------|-----|
| `--dur-fast` | 200ms | Colour, underline and border changes |
| `--dur` | 300ms | Nav indicator, overlay fade |
| `--dur-slow` | 450ms | Plate lift on hover |
| `--dur-slower` | 600ms | Card flip, entry rise |
| `--ease` | `cubic-bezier(.2,.7,.2,1)` | Decelerating arrival for everything |

- Animate only `transform` and `opacity`. Entries rise in a 50ms stagger.
- `prefers-reduced-motion: reduce` switches off all animations and transitions.

### Elevation & z-index

- One shadow, `--lift`, used for hovered plates, the daily plate, the article figure and flashcards.
- z-index scale: header `40`, overlay `60`, grain `90`, skip link `100`.

---

## Component Specs

### Links & buttons
- **Primary button** (`.btn`): ink fill, paper text, 2px radius. Hover turns it accent; press moves it down 1px.
- **Text link** (`.link`): 600 weight with an accent underline. `.link.quiet` uses a rule-colour underline.
- Only one primary action per view.
- Every clickable element has a visible hover, press (`:active`) and `:focus-visible` state. The focus ring is 2px accent with a 3px offset.
- **Targets:** on coarse pointers, controls are at least 44×44px. Text links, subject filters and nav links get the larger hit area from an invisible `::before`, so they look the same. Inputs, buttons and links also get `touch-action: manipulation`.

### Entry card (`.entry`)
- It is a link to `#t-NNN`, not a button.
- Contents: the sketch plate, then the meta line (`No. 001 · Subject`), the serif title and the summary.
- No border or box around the card. Hover lifts the plate and underlines the title in accent.
- Wide cards add "Read the entry →".

### Sketch plate (`.plate`)
- White background with a 16px graph grid and a 1px `--rule` border.
- Stroke classes: `.ln` (ink 1.6px), `.ln2` (ink at 45%), `.fl` (ink at 7%), `.fl2` (accent-soft at 24%).
- Markers `.mk` use an accent circle with `--on-accent` text.

### Forms
- Every input and select has a **visible label** (`.field-lbl`, 12px mono), never a placeholder alone.
- Search updates results as you type (debounced 120ms) and announces the count through `aria-live`.
- An empty search shows a message, a suggestion, and example queries you can click.

### Article overlay (`.detail`)
- Full-screen and deep-linked (`#t-NNN`).
- While it is open, the header, main content and footer are `inert`. Focus moves to the title and returns to the entry that opened it.
- Esc, Back or browser back closes it. ← / → move between entries.

### Flashcard (`.flip`)
- Styled as an index card: rules in `--grid` and an accent margin line.
- Only the visible face is in the accessibility tree (`aria-hidden` flips with the card).
- Keyboard: Space flips, ← review again, → I know it.

---

## Navigation
- Hash routes: `#explore`, `#cards`, `#about`, `#t-NNN` (`deep-linking`).
- The current view is marked with `aria-current="page"` and an accent underline.
- Switching views moves focus to that view's h1 and restores the scroll position it had before (`focus-on-route-change`, `state-preservation`).
- Heading order per view: one h1, then h2, then h3, with no skipped levels.

---

## Anti-Patterns (Do NOT Use)

- ❌ A second accent colour, colour-coded subjects, or a purple/blue gradient look
- ❌ Mono or uppercase-tracked styling on Bengali text
- ❌ Text smaller than 12px, or text colour lighter than `--ink-3`
- ❌ Raw hex values in components
- ❌ Emoji as icons (use inline SVG with `aria-hidden`)
- ❌ Placeholder-only inputs
- ❌ Boxed "border + shadow + white" cards in the grid
- ❌ A sudden dark section on this light page (build a full dark theme or none)
- ❌ Animating width, height, top or left, or ignoring reduced motion

---

## Pre-Delivery Checklist

- [ ] Contrast: body ≥ 4.5:1 (every text token above passes on `--paper`)
- [ ] Visible focus on every control; sticky UI never hides the focused element
- [ ] Coarse-pointer targets ≥ 44×44px
- [ ] Tested at 375px, 768px, 1024px, 1366px and in phone landscape, with no horizontal scroll
- [ ] Both languages checked, including Bangla labels, digits and line heights
- [ ] Reduced motion switches off entry, flip and overlay motion
- [ ] Deep links, back button and focus return work for entries and views
- [ ] No JS console errors

**Not yet covered:** dark mode. If it is added, the `#fff` fills inside sketch SVGs need a mapping to `--plate`, and contrast must be re-verified for the dark palette.
