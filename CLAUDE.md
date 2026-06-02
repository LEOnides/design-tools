# design-tools — CLAUDE.md

Personal toolkit of standalone HTML tools for Design Leaders by leonides delgado.  
Target audience: Spanish-speaking UX/product design managers ("Design Leaders").

---

## Repository layout

```
design-tools/
├── linkedin-formatter.html   # Unicode bold/italic formatter for LinkedIn posts
└── timer.html                # Design-session timer (libre + minuto-a-minuto modes)
```

**There is no build system, no package.json, no framework.**  
Every tool is a single, fully self-contained `.html` file. The only external dependency is Google Fonts loaded via `<link>` tag. Open any file directly in a browser — it works with no server.

---

## Adding a new tool

1. Create a new `.html` file in the repo root.  
2. Follow the brand conventions below (CSS variables, Fira Sans, minimum 16 px, Spanish UI text).  
3. Copy the header/footer markup pattern from an existing tool so the brand identity is consistent.  
4. Keep everything self-contained — no separate `.css` or `.js` files, no npm dependencies.

---

## Brand identity & design system

### Colors (CSS custom properties)
```css
--red:        #FF0033   /* primary accent — buttons, active states, brand name bold */
--black:      #0D0F10   /* page background (dark theme) */
--yellow:     #FFD100   /* secondary accent — "Pro Tips", hero em text */
--white:      #FAFAFA   /* foreground text on dark surfaces */
--gray-dark:  #1A1D1F   /* input/output panel backgrounds */
--gray-mid:   #8A8D91   /* secondary text, labels */
--gray-light: #E5E5E5   /* light-mode border reference (currently unused globally) */
```

`timer.html` adds a full light/dark palette using `prefers-color-scheme` and a richer set of surface tokens (`--bg`, `--surface`, `--surface2`, `--surface3`, `--text-1`, `--text-2`, `--text-3`, etc.). Adopt this pattern for new tools that need dark-mode support.

### Typography
- **Font:** Fira Sans, loaded from Google Fonts. Import weights 300, 400, 500, 600, 700, 900 as needed.
- **Minimum body size:** 16 px — never go below 16 px for any readable text element.
- **Labels / eyebrows:** uppercase, letter-spacing 0.08–0.14em, font-weight 700.
- **Hero headings:** font-size 42px+, font-weight 900, letter-spacing −1px, line-height ≈1.05.
- **Monospace fallback** (code snippets): `font-family: monospace`.

### Logo / brand mark
```html
<span class="brand-name">leonides<strong>delgado</strong></span>
```
- "leonides" → `font-weight: 400`, color `var(--white)`  
- "delgado" → `font-weight: 900`, color `var(--red)`  
- The `<strong>` tag drives the visual weight; never separate them with a space.

### Border-radius
- Buttons, pills, tags: `2–3px` (sharp / near-brutalist).
- Cards (timer.html): `22px` (softer mobile-native feel).

### Background grid (linkedin-formatter)
```css
background-image:
  linear-gradient(var(--grid-line) 1px, transparent 1px),
  linear-gradient(90deg, var(--grid-line) 1px, transparent 1px);
background-size: 40px 40px;
```
`--grid-line: rgba(255,255,255,0.04)` — very subtle; use it for dark-background tools.

---

## Tool summaries

### `linkedin-formatter.html`

Converts Markdown-style `**bold**` and `_italic_` markers to Unicode Mathematical Bold/Italic characters that survive LinkedIn's paste stripping.

**Key functions (vanilla JS):**
| Function | Purpose |
|---|---|
| `convert(src)` | Parses `**`/`_` markers and maps each character through Unicode lookup tables |
| `liCount(str)` | Counts characters using LinkedIn's rule: supplementary-plane chars (codepoints > U+FFFF) count as 2 |
| `refresh()` | Called on every `input` event; updates output panel, character counter, and progress bar |
| `wrapSel(open, close)` | Wraps the current textarea selection with the given markers (toolbar N/I buttons) |
| `doCopy()` | Copies output to clipboard via `navigator.clipboard.writeText` with a 3-second success state |

**Character limit:** 3,000 LinkedIn characters. Bar turns yellow at >85 %, red when over limit.

**Unicode maps** are built at module load into `boldU`, `italicU`, `boldItalicU` objects covering A–Z, a–z, and digits 0–9.

---

### `timer.html`

Design-session countdown timer with two modes:

| Mode | Description |
|---|---|
| **Timer libre** | Single countdown; preset buttons (5–60 min) or custom input; play/pause/reset; manual "save to log" |
| **Minuto a minuto** (sequence) | Queue of named activities each with their own duration; runs through them in order automatically |

**State variables:**
```
mode        'libre' | 'secuencia'
total       seconds in current countdown
remaining   seconds left
running     boolean
ticker      setInterval handle
schedule    [{id, name, mins}]  — sequence items
seqState    'idle' | 'running' | 'paused' | 'done'
seqIdx      current sequence index (-1 = before start)
logEntries  [{id, secs, status, name, time}]
```

**Key functions:**
| Function | Purpose |
|---|---|
| `buildTicks()` | Draws 60 SVG tick marks + minute-number labels on the clock face at init |
| `arcPath(rem, tot)` | Returns an SVG path for the red pie-slice arc representing remaining time |
| `render()` | Updates display text, arc, status label, and activity label; called every second and on state changes |
| `startClock()` / `pauseClock()` / `stopClock()` | Core timer control |
| `onTimerEnd()` | Handles natural expiry: logs the completed segment, beeps, then calls `seqAdvance()` in sequence mode |
| `seqAdvance(skipped)` | Moves to the next sequence item or marks the session done |
| `addLog(secs, status, name)` | Appends an entry to the session log (displayed at the bottom) |
| `beep()` / `beepNext()` / `beepDone()` | Web Audio API tones: triple beep on end, single on next activity, ascending chord on session complete |

**Dark mode:** fully supported via `prefers-color-scheme: dark` overriding the same CSS custom properties.

---

## Coding conventions

- **Vanilla everything.** No libraries, no transpilers, no bundlers.
- **Inline JS at the bottom** of each file inside a single `<script>` tag.
- **Inline CSS in `<style>` in `<head>`**, organized with section comment banners (`/* ── HEADER ── */`).
- **Spanish UI strings** everywhere: button labels, placeholders, status messages, footer taglines.
- **`box-sizing: border-box`** + reset (`margin: 0; padding: 0`) applied globally via `*, *::before, *::after`.
- **CSS custom properties** for every color and shadow — never hardcode a hex value outside `:root`.
- **`font-variant-numeric: tabular-nums`** on any element displaying a changing number (timer display, character counter).
- **Responsive:** `@media (max-width: 768px)` breakpoint; reduce padding, collapse multi-column layouts to 1-column, optionally hide non-critical elements (e.g., `.hero-tip`).
- **No comments in CSS/JS** unless the logic is genuinely non-obvious (e.g., the LinkedIn double-count rule or the SVG arc math).

---

## Git workflow

- Commit prefix convention: `feat:` for new tools/features, `fix:` for corrections.
- Feature branches follow the pattern `claude/<description>`.
- Commits are in English even though the tool UI is in Spanish.
- No CI, no tests, no linter — validation is visual (open the file in a browser).

---

## Content & tone (for copy/UI text)

All UI text is in **Spanish (Mexico/Latin America)**:
- Informal but professional register ("Escribe tu post aquí…", "¡Copiado!")
- Brand tagline: `"18 años en las trincheras. Ahora es tu turno."` — keep it in the footer.
- Tool eyebrows follow the pattern: `"Pro Tips para Design Leaders"`
- Status/feedback copy: actionable and brief ("Pega directo en LinkedIn.", "Acorta el texto o reduce el formato.")
