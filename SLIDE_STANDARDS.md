# Business EFE — Slide Standards

This file is the single source of truth for all design and UX decisions across Business EFE lessons. Update it whenever a new standard is established.

---

## Layout

### Columns — Example placement and split

#### When N (exercises) is EVEN
- **Example spans the full width** — placed in a dedicated `<div id="hwXX-example">` above the `.two-col` grid, NOT inside a column.
- Exercises split **equally**: N/2 left, N/2 right → `i < N/2`.
- In HTML: add `<div id="hwXX-example"></div>` between the instruction `<p>` and `<div class="two-col">`.
- In JS: `exEl.innerHTML=''` on build/reset; append example to `exEl`, not to `left`.
  - N=8 → 4 left + 4 right, example full-width above
  - N=10 → 5 left + 5 right, example full-width above

#### When N (exercises) is ODD
- **Example goes in the LEFT column**, at the top (as a block inside `hw43-left` / similar).
- Formula: `split_index = floor(N/2)` → left gets `floor(N/2)` exercises, right gets `ceil(N/2)`.
- Counting total visible blocks per column: left = Example + floor(N/2), right = ceil(N/2). For odd N the total blocks difference is 1 (left heavier), which is acceptable.
  - N=5 → `i < 2` → left: Example+2 (3 blocks), right: 3 (3 blocks) — equal total ✓
  - N=11 → `i < 5` → left: Example+5 (6 blocks), right: 6 (6 blocks) — equal total ✓

### HW Slide Structure
Every HW slide must have:
1. Banner: `📝 Homework` (no slide numbers, no pills)
2. `<h2>` title: e.g. `HW 1 – Countries & Continents` (`font-size:1.75rem; font-weight:800`)
3. `<p class="inst-light">` instruction immediately below the title
4. Content (exercise)
5. Check / Reset buttons (`btn-check` / `btn-reset`)
6. Result div

---

## Typography

### Minimum font size
**Never use a font size smaller than `1.05rem`** for any content text in a slide body.
- `1.05rem` = `.inst-light` baseline — this is the floor, not a target.
- Exception: decorative UI-only labels (e.g. banner nav pills, dot counters) may use smaller sizes.

### Classes
| Class | Size | Weight | Use |
|---|---|---|---|
| `.instruction` | `1.08rem` | `700` (bold) | Action prompts, task labels |
| `.inst-light` | `1.05rem` | `400` (normal) | Explanatory/grammar text |
| `h2` | `1.75–1.85rem` | `800` | Slide titles |
| `.ex-row` | `1.06rem` | — | Exercise item rows |
| `.ans-input` | `1.05rem` | `700` | Text input fields |

### Instructions
- `.instruction` (bold): for action labels like "Choose the correct word."
- `.inst-light` (normal): for grammar explanations. Only the specific word/form being taught goes in `<strong>`.
- Example: `<p class="inst-light">Use <strong>"from"</strong> or a nationality adjective.</p>`

---

## Colours

### Semantic colour rules — strictly enforced
| Colour | Hex | Use ONLY for |
|---|---|---|
| Green | `#1A7A4A` / `#EEF7F0` | ✅ Correct answers (after Check) |
| Red | `#C4614A` / `#FCEEED` | ❌ Wrong answers (after Check) |
| Navy | `#1A5080` | Selected chips/buttons (active state) |
| Orange | `#C45020` | Example boxes, accent borders |
| Purple | `#7B2FBE` | "from + Country" in rewrite exercises |
| Blue-teal | `#0E5C7A` / `#8BB0D0` | Input underlines, neutral borders |

### DO NOT use green for:
- Default chip backgrounds
- Word bank backgrounds
- Any element that hasn't been checked yet

---

## Word Banks & Chips

### Default chip states
```
Free:     background:#EEF5FA; color:#1C2B3A; border:1.5px solid #C5A8E0; opacity:1
Selected: background:#1A5080; color:#fff;    border-color:#1A5080; transform:scale(1.05)
Used:     background:#f0eef8; color:#bbb;    border-color:#ddd; text-decoration:line-through; opacity:0.65
```

### Word bank container
```css
background: #F5F0FF;
border: 1px solid #C5A8E0;
border-radius: 10px;
padding: 14px 18px;
```

### Behaviour
- **Click free chip** → selects it (navy)
- **Click zone/box** → places chip; chip becomes "used" (strikethrough)
- **Click used chip** → picks it back up (removes from zone, reselects)
- **Click selected chip again** → deselects (returns to free)
- **Zone highlighting** → `border-color:#1A5080; background:#EEF5FA` (NOT green)
- Student can **always redo** — never lock chips after Check

---

## Check / Correction Behaviour

### Multiple choice (select one option)
- On Check: color the **selected button only** — green if correct, red if wrong.
- **Do NOT reveal the correct answer** by highlighting it.
- Student sees red → understands they were wrong → can try again after Reset.

### Sort / drag to zone
- On Check: color each placed chip green (correct zone) or red (wrong zone).
- Student can click a red chip to retrieve it and re-place it.

### Text inputs
- On Check: `border-bottom-color` → green (correct) or red (wrong).
- `color` → green or red to match.

### Text input sizing
- **All inputs in an exercise must be the same fixed width** — no `min-width`/`max-width`, use `width` only.
- Use `flex-shrink:0` so the input never gets squashed in a flex row.
- Also use `flex-shrink:0` on the question/error text span — **do NOT use `flex:1`** on it. `flex:1` grabs all remaining space and pushes the input to the next line.
- Size the input so that the entire row (number + question text + input) fits on one line for the majority of sentences. Accepting that the 1–2 longest sentences may wrap is fine.
  - Target: `width:220px` for exercises where question texts are typically 18–35 chars.
  - Students type into the input — text scrolls within it if the answer is long, which is acceptable.

### Cross-out / "Select the Correct Option" exercises
When the book shows a "Cross Out the Incorrect Word" exercise, implement it as **Select the Correct Option** — same style as the in-lesson exercise (e.g. slide 6 / s7).

**Title (h2):** `HW X – Select the Correct Option` — plain `<h2>` with no inline style, matching the lesson slide.

**Instruction (`<p class="instruction">`, bold):**
> Click the correct word that fits the sentence — the other will be crossed out automatically.

**Visual style — buttons, not span chips:**
```css
/* Default state */
border:2px solid #BDD4F0; border-radius:8px; padding:4px 14px;
font-size:1.05rem; font-weight:700; background:#fff; color:#1C2B3A;

/* Selected (correct word chosen) */
background:#EEF7F0; color:#1A5C2A; border-color:#1A7A4A;

/* Faded (not chosen) */
background:#f5f5f5; color:#bbb; border-color:#e0e0e0;
text-decoration:line-through; opacity:0.6;
```

**Pick interaction (`hwXXPick`):**
1. Reset both buttons to default
2. Set clicked button → selected style (green)
3. Set other button → faded + line-through

**Check behaviour (`checkHWXX`) — smarter than the lesson slide:**
- **Correct answer**: leave as-is (already green — no change needed). Count score.
- **Wrong answer**: turn selected button **red** (`background:#FCEEED; color:#C4614A; border-color:#C4614A`). Reset the **other** button to **default** (neutral, clickable) so student can switch without resetting.
- **Unanswered**: leave as-is (don't touch).
- Do NOT reset all items to neutral before checking — only modify wrong ones.

**Why this matters:** student sees exactly which items are wrong (red) and can click the correct word immediately to fix them, without needing to hit Reset first.

---

## Audio

- Only add audio if the `.mp3` file **actually exists** in the folder.
- Pattern: `<audio controls style="flex:1;width:100%;accent-color:#14607A;"><source src="X.mp3" type="audio/mpeg"></audio>` inside a gradient container.
- Do not add audio placeholders (`audio-placeholder` divs) to HW slides unless confirmed.

---

## Example Boxes

```html
<div style="background:#FFF4EE; border-left:3px solid #C45020; border-radius:10px; padding:8px 14px; margin-bottom:12px; font-size:1.05rem;">
  <strong>Example:</strong> ...
</div>
```
- Font: always `1.05rem` minimum.
- Background: `#FFF4EE` (warm cream).
- Border: orange left border (`#C45020`).
- Label: `<strong>Example:</strong>` — always say "Example", never "a" or a number.

---

## Sentence Colouring (Rewrite Exercises)

For exercises that switch between "from + Country" and nationality adjective:
- **"from [Country]"** → `color:#7B2FBE` (purple), bold
- **Nationality adjective** → `color:#C45020` (orange), bold
- Rest of sentence → `color:#1C2B3A` (black), normal weight

---

## Banner Standard

```html
<div class="banner" style="background:[COLOR];color:#fff;">EMOJI Category</div>
```
- Format: `emoji + space + Category name` only.
- **No slide numbers**, no pills, no extra info in the banner.
- For HW slides: `📝 Homework`
- For Key Language: `🔑 Key Language`
- For Listening: `🎧 Listening`

---

## Slide IDs & Navigation

- `slideIds` and `slideLabels` arrays **must stay in sync** (same length, same order).
- When deleting a slide: remove from HTML, `slideIds`, and `slideLabels`, and renumber HW `<h2>` titles.
- `goToId(id)` uses `slideIds.indexOf(id)`.

---

## HW Slide Numbering

When slides are added or deleted, **renumber all HW `<h2>` titles** sequentially:
`HW 1 – Title`, `HW 2 – Title`, etc.

---

## Notes Panel — Required on Every Lesson

Every lesson file must ship with a fully-featured notes panel — not just the "📝 Notes" toggle and textarea. This is a checklist, not optional polish.

### Required pieces
1. **`#notesBtn`** — fixed top-right toggle button (`📝 Notes` / `✕ Close`).
2. **`#notesPanel`** with **`#notesToolbar`** containing, in this order:
   - Bold (`notesFormat('bold')`) and Underline (`notesFormat('underline')`) buttons
   - A thin divider (`<span style="width:1px;background:rgba(255,255,255,.2);...">`)
   - Four highlight-color dots (`notesHilite('#FFE600')`, `'#AAFFC3'`, `'#FFB3C1'`, `'#BFE0FF'`)
   - Another divider
   - **`🗑 Clear all`** button (`clearAllNotes()`) — erases every saved note for the lesson, with a `confirm()` guard
   - **`📄 Save as PDF`** button (`downloadNotes()`) — exports all non-empty notes as a printable HTML page
3. **`#notesTA`** — the contenteditable notes area, per-slide persistence via `localStorage` keyed as `notes_l<N>_<slideIndex>` (e.g. `notes_l6_3`).
4. **`#notesTA [style*="background-color"]{color:#1a1a1a!important;}`** in the CSS — without this, highlighted text inherits the panel's white font color and becomes unreadable against the light highlight colors. This one-line rule is mandatory whenever `#notesTA` has `color:#fff` (or any light color) as its base.

### `downloadNotes()` — required behavior
- Calls `saveNotes()` first to flush the current slide's in-progress edits to `localStorage`.
- Loops over `slideIds`, reading `localStorage.getItem('notes_l<N>_'+idx)` for each index, skipping empty ones.
- If nothing was written, `alert('No notes to save yet!')` and stop.
- Otherwise builds a standalone printable HTML document (own `<style>`, one `.card` per slide with notes, `.card-header` = `Slide N — label`, `.card-body` = the saved note HTML), opens it in a new tab, and calls `window.print()` after a short delay.
- The exported page's own CSS **must also include** `.card-body [style*="background-color"]{color:#1a1a1a!important}` — the highlight-readability fix applies to the printed/PDF output too, not just the live panel.
- Include a link back to the lesson's GitHub Pages URL (`https://teacher-nanda.github.io/Business-EFE1/<filename>.html`) near the top of the exported page.

Use `Lesson_03_Business_Around_the_World.html`'s `downloadNotes()`/`clearAllNotes()` as the reference implementation — copy and adapt the lesson number and `localStorage` prefix, don't rewrite from scratch.

---

## Language

### Spelling — American English only
**All text in every slide must use American English spelling.** This is non-negotiable and applies to every edit, rewrite, or new section.

- When rewriting or restructuring existing content, **preserve the original spelling exactly** — do not alter words that were already correct.
- Never introduce British spellings (e.g. favourite → favorite, colour → color, travelling → traveling, organise → organize, etc.).
- After any edit that touches article or exercise text, mentally verify no British spellings were introduced.

---

*Last updated: 2026-08-07 (added Notes Panel requirements: Clear All + Save as PDF buttons, highlight-readability CSS fix, downloadNotes() spec)*
