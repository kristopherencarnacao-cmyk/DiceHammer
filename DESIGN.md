# PhosphorDice — Visual Design Spec

A complete reference for reproducing the PhosphorDice look exactly. The source of truth is `index.html` (all CSS is in its second `<style>` block; effects that need JS are in the single `<script>`). Where this doc and the code disagree, the code wins — update this doc.

Live app: https://kristopherencarnacao-cmyk.github.io/DiceHammer/

---

## 1. Concept in one paragraph

A one-screen **phosphor terminal on a curved CRT**. Cold near-black glass, ash-white text, and a single glowing phosphor accent (green by default) that does nearly all the work: borders, labels, lit pips, glows. Everything is **monospace, uppercase, square-cornered and letter-spaced**, like a military/industrial readout (the style is inherited from the "Dominatus Codex" tracker). Two reserved colours sit outside the accent: **gold = critical hits**, **red = failures and 1s**. Motion is **signal-like** — stepped flickers, brightness spikes, small horizontal glitches, scanlines — never bouncy or soft, except the crit "pop".

Design rules that are never broken:
- One accent colour per theme. Gold and red are semantic only (crit / fail) and never change with the theme.
- Buttons and panels have **square corners** (`border-radius:0`). Only dice (12%) and the CRT glass are rounded.
- All text uses the monospace stack. No other font is loaded.
- Labels are uppercase with wide tracking (`.06em`–`.16em`).
- Overlays (scanlines, CRT glass, effects) never block input (`pointer-events:none`).
- Everything must fit one viewport — no page scroll (`html,body{height:100%;overflow:hidden}`).

---

## 2. Design tokens

All defined on `:root`. The variable names are historical (`--brass` is the phosphor accent, not brass) — keep them for compatibility.

### Neutrals
| Token | Value | Use |
|---|---|---|
| `--ash` | `#05070a` | Page background, deepest black, key bases, icon badge fill |
| `--slab` | `#0e1217` | Panel fill (`.frame`) |
| `--raise` | `#151a21` | Die body base |
| `--ink` | `#dfe4ea` | Primary text, pool number |
| `--muted` | `#8792a0` | Secondary labels, idle numbers, missed pips |
| `--rule` | `#232b35` | (reserved hairline) |
| `--deep-shadow` | `rgba(0,0,0,.6)` | Drop shadows |

### Accent (phosphor) — green default
| Token | Green value | Role |
|---|---|---|
| `--brass` | `#55e6a5` | **The accent.** Text glow colour, lit pips, active borders, primary buttons |
| `--seal-hi` | `#8af5c4` | Highlight tint: top of lit gradients, sparkles |
| `--phos-deep` | `#2a9d6c` | Bottom of lit gradients |
| `--brass-soft` | `#0b231b` | Hover / selected fill (very dark tint) |
| `--phos-dim` | `#1f5a45` | Default 1px borders, dashed rules, unlit tray dots |
| `--phos-ink` | `#02140d` | Text/pips placed ON a lit accent fill |
| `--rgb` | `85,230,165` | Accent as an RGB triplet for `rgba(var(--rgb),a)` |
| `--rgb-hi` | `200,255,230` | Pale highlight triplet (bevel glints, flicker band core) |
| `--glow` | `rgba(var(--rgb),.45)` | Strong glow |
| `--glow-soft` | `rgba(var(--rgb),.18)` | Soft glow / hover halo |

**Never hard-code an accent colour.** Always use the tokens or `rgba(var(--rgb),a)` so themes work.

### Semantic (theme-independent)
| Token | Value | Use |
|---|---|---|
| `--gold` | `#f5c451` | Crit count, Lock/Dupe buttons, locked/dupe dice badges |
| `--gold-glow` | `rgba(245,196,81,.45)` | Gold glow |
| `--danger` | `#e04652` | Fail count, 1s outline + skull |
| `--red-glow` | `rgba(224,70,82,.45)` | Red glow |
| `--blood` | `#1f080c` | 1-die gradient bottom |
| `--blood-hi` | `#45121a` | 1-die gradient top |

### Type
```css
--term: ui-monospace,"SF Mono",SFMono-Regular,Menlo,Consolas,"Liberation Mono","Courier New",monospace;
```
`body` = 14px, `color-scheme:dark`.

### Themes (Menu → Theme)
Set by `data-accent` on `<html>` (NOT `data-theme` — the claude.ai artifact host uses that). Green = no attribute. Each theme overrides exactly these eight tokens:

| Theme | `--brass` | `--seal-hi` | `--phos-deep` | `--brass-soft` | `--phos-dim` | `--phos-ink` | `--rgb` | `--rgb-hi` |
|---|---|---|---|---|---|---|---|---|
| green (default) | `#55e6a5` | `#8af5c4` | `#2a9d6c` | `#0b231b` | `#1f5a45` | `#02140d` | `85,230,165` | `200,255,230` |
| cyan | `#4fd8ff` | `#a3ecff` | `#1d8fb4` | `#071d26` | `#1b5266` | `#021219` | `79,216,255` | `205,245,255` |
| blue | `#7a96ff` | `#b3c2ff` | `#3d55c9` | `#0d132e` | `#2b3a7d` | `#050a20` | `122,150,255` | `215,224,255` |
| violet | `#b685ff` | `#d8bdff` | `#7140c9` | `#190e2c` | `#4b307c` | `#10051f` | `182,133,255` | `232,215,255` |
| magenta | `#ff63d4` | `#ffa3e7` | `#b8308f` | `#2a0920` | `#6e2458` | `#1d0216` | `255,99,212` | `255,214,243` |
| orange | `#ff8f3f` | `#ffbd8a` | `#c25a17` | `#2a1305` | `#6e3a17` | `#1c0b01` | `255,143,63` | `255,224,196` |

Recipe for a new theme: `--brass` = saturated mid-bright hue; `--seal-hi` ≈ brass mixed ~45% toward white; `--phos-deep` ≈ brass at ~60% brightness; `--phos-dim` ≈ brass at ~30% brightness, slightly desaturated; `--brass-soft` ≈ brass at ~10% brightness; `--phos-ink` ≈ near-black tinted with the hue. Saved in `localStorage['dicehammer-theme']`; a tiny inline script right after `<title>` applies it before first paint (no green flash).

---

## 3. Typography scale

All `font-family:var(--term)`. Uppercase unless noted.

| Element | Size | Weight | Tracking | Colour / effect |
|---|---|---|---|---|
| App title `.eyebrow` | 24px (20px ≤380px wide; 20px landscape; 17px short landscape; `24px + u*.5` large landscape) | 700 | `.16em` (`.12em` narrow) | `--brass`, `text-shadow:0 0 12px var(--glow),0 0 2px rgba(var(--rgb),.8)`, line-height 1 |
| Title cursor `.cursor` | block `.55em × .9em`, `margin-left:.2em`, `vertical-align:-.1em` | — | — | `--brass` fill, `box-shadow:0 0 8px var(--glow)`, blink 1.1s `steps(1)` |
| Big numbers `.big` | `var(--big)`: 38px default; 32px ≤380w; 30px ≤620h / landscape; 28px on L tray; 24px short landscape; `40px + u` large landscape | 700 | normal | `tabular-nums`, `height` = `line-height` = font-size |
| Key label `.k-lbl` | 10px | 600 | `.14em` | `--muted`, prefixed `// ` in `--phos-dim` (prefix removed in stats and crit row) |
| Buttons `.btn` | 11px, line-height 1.15 | 600 | `.08em` | `--brass` |
| Target labels (`1+`…) | 12px | 700 | `.08em` | `--muted`; selected → `--brass` + `text-shadow:0 0 8px var(--glow)` |
| Hint line `.hint` | 10.5px | normal | `.08em` | `--muted`, prefixed `> ` in `--brass` |
| Menu items | 11.5px / 1 | 600 | `.1em` | `--ink`; hover `--brass` |
| Log title `.h-title` | 11.5px | 600 | `.06em` | `--ink` |
| Log hits `.h-hits` | 22px / 1 | 700 | — | `--brass` + `0 0 8px var(--glow)` |
| Guide heading | 17px / 1.25 | 700 | `.02em` | `--ink`, NOT uppercase, `text-wrap:balance` |
| Guide body | 13.5px / 1.55 | normal | — | `--ink`, NOT uppercase; `<b>` = `--brass` 600 |
| Guide citation | 10px / 1.4 | normal | `.03em` | `--muted`; links `--brass`, underline `--phos-dim`, offset 2px |

Terminal prefixes are part of the voice: `// ` before labels, `> ` before hints and empty states, `[ … ]` around guide nav buttons (via `::before/::after` at 60% opacity).

---

## 4. Page layers (z-index stack, back to front)

1. **`body` background**: `--ash` plus two gradients:
   `radial-gradient(ellipse 110% 70% at 50% 0%, rgba(var(--rgb),.10), transparent 60%)` (accent haze from the top) and `radial-gradient(ellipse 120% 90% at 50% 50%, transparent 55%, rgba(0,0,0,.55) 100%)` (vignette).
2. **`.app`** — all UI.
3. **`.crt-band`** (z 9997) — flicker band, invisible until a flicker.
4. **`body::before`** (z 9998) — slow scan sweep: 20%-tall band, `linear-gradient(180deg,transparent,rgba(var(--rgb),.035),transparent)`, `translateY` 0 → 600% over 7s linear infinite.
5. **`body::after`** (z 9999) — scanlines + vignette: `repeating-linear-gradient(180deg,rgba(0,0,0,.2) 0 1px,transparent 1px 3px)` over `radial-gradient(ellipse 120% 100% at 50% 50%,transparent 60%,rgba(0,0,0,.42) 100%)`.
6. **`.crt-glass`** (z 10000) — curved glass (see §5). Hosts the cable-spark effect (`.arc`, z 4 inside it).
7. Overlays: `.scrim` z 20, `.guide` / `.sheet` z 21, `.menu` z 30 (inside the header).

All of layers 3–6 are `position:fixed; pointer-events:none`.

---

## 5. CRT glass

### Phones and small screens (default)
`.crt-glass{position:fixed;inset:0;border-radius:clamp(18px,4vw,42px)/clamp(24px,5vw,56px)}` with
```css
box-shadow: 0 0 0 80px #000,                     /* black bezel outside the rounded corners */
            inset 0 0 0 1px rgba(var(--rgb),.10), /* faint phosphor rim */
            inset 0 0 26px 6px rgba(0,0,0,.85),    /* tight inner edge shade */
            inset 0 0 120px 20px rgba(0,0,0,.45);  /* wide barrel shade */
```
≤560px wide uses lighter insets (`18px 4px .85` and `70px 12px .4`).
Children:
- `.crt-bulge`: `radial-gradient(ellipse 140% 120% at 50% 50%, transparent 58%, rgba(0,0,0,.28) 78%, rgba(0,0,0,.6) 100%)` + `radial-gradient(ellipse 90% 70% at 50% 45%, rgba(var(--rgb),.035), transparent 70%)`.
- `.crt-glare`: `radial-gradient(ellipse 55% 30% at 28% 10%, rgba(220,240,255,.07), transparent 70%)` + diagonal sheen `linear-gradient(115deg, transparent 62%, rgba(220,240,255,.025) 70%, transparent 78%)`.

### Large landscape (≥1000×600, landscape, aspect ≥ 13/10): SVG tube
The rounded-rect box-shadow is replaced by an inline SVG (`#crtSvg`) drawing a **continuous bowed outline** — every edge curves outward slightly and blends tangentially into oval corners (no flat runs). Inset **16px** from the browser edge. Rebuilt on resize by `tube(W,H,m)`:
- Edge bow: top/bottom `w*.011`, sides `h*.016` (quadratic curves through the mid-points).
- Corners: cubic Béziers, radii `rx=w*.065`, `ry=h*.095`, handle factor `k=.56`, control points aligned to the bowed-edge tangents (G1 continuous).
- Layers: black fill outside (`#crtMask`, even-odd rect minus tube); inside the clip, two blurred black strokes along the outline (`stroke-width:150, opacity .55, blur 22` and `stroke-width:40, opacity .9, blur 6`) for barrel shading; rim strokes: main `rgba(var(--rgb),.24)` 2px at m=16, inner `rgba(var(--rgb),.07)` 1px at m=22, outer `rgba(var(--rgb),.10)` 1px at m=13.
- `.crt-bulge` and `.crt-glare` are clipped to the same path (`clip-path:url(#crtClip)`).
- Content padding grows so dice never sit under the curve: `--pad: calc(16px + clamp(34px,5.5vmin,64px))`.

---

## 6. The bracketed panel `.frame`

Signature container used for the tray, menu, roll log sheet and guide. Square, slab-filled, with **L-shaped accent corner brackets** drawn by eight background gradients:
```css
.frame{--fr:var(--brass);--bk:12px;--bt:2px;               /* bracket colour, arm length, thickness */
  background:
    linear-gradient(var(--fr),var(--fr)) 0 0/var(--bk) var(--bt) no-repeat,
    linear-gradient(var(--fr),var(--fr)) 0 0/var(--bt) var(--bk) no-repeat,
    linear-gradient(var(--fr),var(--fr)) 100% 0/var(--bk) var(--bt) no-repeat,
    linear-gradient(var(--fr),var(--fr)) 100% 0/var(--bt) var(--bk) no-repeat,
    linear-gradient(var(--fr),var(--fr)) 0 100%/var(--bk) var(--bt) no-repeat,
    linear-gradient(var(--fr),var(--fr)) 0 100%/var(--bt) var(--bk) no-repeat,
    linear-gradient(var(--fr),var(--fr)) 100% 100%/var(--bk) var(--bt) no-repeat,
    linear-gradient(var(--fr),var(--fr)) 100% 100%/var(--bt) var(--bk) no-repeat,
    linear-gradient(180deg,rgba(var(--rgb),.035),transparent 40%),
    var(--slab);
  border:1px solid var(--phos-dim);border-radius:0;
  box-shadow:inset 0 0 22px rgba(var(--rgb),.05),0 0 0 1px rgba(0,0,0,.6),0 12px 30px var(--deep-shadow)}
```
Bracket arm `--bk`: 12px default, 8px menu, 16px sheet and guide.

---

## 7. Buttons

### `.btn` (base)
`background:transparent; border:1px solid var(--phos-dim); border-radius:0; color:var(--brass); font:600 11px/1.15 var(--term); uppercase; letter-spacing:.08em; transition: background/box-shadow/border-color .15s`.
- Hover: `background:var(--brass-soft); border-color:var(--brass); box-shadow:0 0 12px var(--glow-soft)`.
- Active: solid `background:var(--brass); color:var(--phos-ink)` (a full "lit" flash).
- Disabled: `opacity:.35`.
- `.btn.primary`: solid accent fill, `--phos-ink` text, `0 0 14px var(--glow-soft)`; hover → `--seal-hi`.

### Variants
- **Step buttons** `−` / `+`: 34×34 square, 18px bold (40px+ on large landscape).
- **Menu button**: `.btn.hist`, 30px tall, burger icon of three 12×2px bars, 3px gap. Expanded → hover styling persists.
- **Action row** (Sort, Re-roll 1s, Re-roll fails, Remove fails): equal flex, `height: calc(40px + var(--grow))`, 6px gaps. Labels may wrap to two lines.
- **Crit row** (gold): `height: calc(34px + var(--grow))`, `border-color: rgba(245,196,81,.35)`, `color: var(--gold)`; hover `background: rgba(245,196,81,.08)`, gold border, gold glow.
  - **Lock crits** `.tog`: label + bold state word (`Off`/`On`, 75% opacity). Pressed → solid gold fill, text `#1a1200`, gold glow; hover `#ffd977`.
  - **Dupe crits**: plus icon, gold.
  - **Undo**: stays accent-coloured (not gold), `flex:0 0 auto`, 12px side padding.
- **Icons** `.ic`: 12×12 CSS-mask icons tinted by `currentColor`, `margin-right:6px`, `vertical-align:-1px`. Shapes (16×16 viewBox):
  - lock: shackle `M5 7.5V5.2a3 3 0 0 1 6 0v2.3` (stroke 2) + body `rect x3 y7.5 w10 h7 rx1.4`
  - plus: `M8 2.5v11M2.5 8h11` (stroke 3)
  - undo: chevron `M6 3.5 2.5 7 6 10.5` + hook `M3 7h6.5a4 4 0 0 1 0 8H7` (stroke 2.2)

### Checkbox `.chk` (crit threshold, guide "don't show")
22×22 square, 1px `--phos-dim` border, transparent. Hover: accent border, `--brass-soft` fill. Checked: `linear-gradient(155deg,var(--seal-hi),var(--brass) 60%,var(--phos-deep))`, `--seal-hi` border, `0 0 10px var(--glow)`, and a square-capped checkmark (`M3 8.5l3.2 3.2L13 4.8`, stroke 2.4) in `--phos-ink`.

---

## 8. Dice

Dice are a 3×3 CSS grid of `<i>` pips; a pip is shown by adding class `s`. Pip index layout (row-major 0–8):
```
1:[4]  2:[0,8]  3:[0,4,8]  4:[0,2,6,8]  5:[0,2,4,6,8]  6:[0,2,3,5,6,8]
```
Geometry: `border-radius:12%; padding:18%; gap:6%; aspect-ratio:1`; pips are `border-radius:50%`.

### States (tray cells `.cell`, target dice `.die`)
| State | Body | Outline / shadow | Pips |
|---|---|---|---|
| Empty slot | transparent with a single centred dot `radial-gradient(circle,var(--phos-dim) 0 1.5px,transparent 2px)` | `inset 0 0 0 1px rgba(var(--rgb),.06)` | hidden |
| Die (`.on` / `.die`) — "dark glass" | `linear-gradient(155deg,rgba(var(--rgb),.15),rgba(var(--rgb),.03) 55%,rgba(0,0,0,.3)), var(--raise)` | `inset 0 0 0 1px var(--phos-dim), inset 1px 1px 0 rgba(var(--rgb-hi),.09), inset -2px -3px 6px rgba(0,0,0,.45), 0 3px 6px var(--deep-shadow)` (bevel + drop) | `--brass`, `0 0 4px var(--glow)` |
| Hit | same | outline → `var(--brass)` + `0 0 10px var(--glow-soft)` halo | lit |
| Miss | same at **30% opacity** | — | `--muted`, no glow |
| Crit (`.six`, any face ≥ crit threshold) | **solid lit** `linear-gradient(155deg,var(--seal-hi),var(--brass) 55%,var(--phos-deep))` | `inset 0 0 0 1px var(--seal-hi)`, `0 0 14px var(--glow)`; `::after` halo ring (`inset:-3px`, `0 0 14px 3px var(--glow)`) that pulses | dark `--phos-ink`, inset shadow (engraved) |
| 1 (`.one`) | `linear-gradient(155deg,var(--blood-hi),var(--blood))`, 90% opacity | `inset 0 0 0 1px var(--danger)`, `0 0 10px var(--red-glow)` | hidden; a **skull** SVG (72% of the cell, `--danger`, `drop-shadow(0 0 3px var(--red-glow))`) |
| Locked crit | crit look + `outline:1.5px solid var(--gold); outline-offset:1px` | + gold badge | |
| Duped die (value stored as 10+face) | normal die | `inset 0 0 0 1px var(--gold)` + gold badge | lit |
| Unrolled cells after a roll | `.grid.rolled .cell:not(.on){opacity:.3}` | | |

**Corner badges** (locked / dupe): `::before` circle `top:-9%; right:-9%; 38% size`, `--ash` fill, 1px gold border, `0 0 6px var(--gold-glow)`, gold lock or plus icon at 62%. They match the button icons exactly.

**Skull path** (24×24 viewBox, also used in the app icon):
`M12 2a8 8 0 0 0-8 8c0 2.6 1.2 4.6 3 5.8V19a1 1 0 0 0 1 1h1v-2h1.5v2h3v-2H15v2h1a1 1 0 0 0 1-1v-3.2c1.8-1.2 3-3.2 3-5.8a8 8 0 0 0-8-8zM6.6 11a2.2 2.2 0 1 0 4.4 0a2.2 2.2 0 1 0-4.4 0zM13 11a2.2 2.2 0 1 0 4.4 0a2.2 2.2 0 1 0-4.4 0zM12 13.6l-1.3 2.3h2.6z` (even-odd fill)

### The tray `.grid.frame`
Square, sized by JS to `min(stage width, stage height)`. `grid-template: repeat(N,1fr)/repeat(N,1fr)`. Sizes: S 5×5 (gap 6px), **M 8×8 default** (gap 4px, padding 8px), L 12×12 (gap 3px, padding 7px; cells `padding:15%; gap:8%; radius:16%`). Dice fill in reading order from the top-left.

### Target dice as "keys"
Each of the six targets is a die sitting on a fixed **key base** (`.key::before`): same 12% radius, 1px `--phos-dim` border with **transparent top edge**, `linear-gradient(180deg,transparent 40%,rgba(var(--rgb),.10)), var(--ash)` fill, `0 2px 0 rgba(0,0,0,.5)`. The die rises off it and presses back (`keyBob`, below) in a **shuffled order** (delays `order*0.55s`). Max width 54px (+grow), 46px landscape, 42px when ≤620px tall, 36/30px on very short landscape, `58px + u*1.5` large landscape.
- Selected target: lit-crit die body, `--phos-ink` pips, base border turns `--brass` with `0 4px 12px var(--glow-soft)`; **only the selected key keeps animating**, the others go still.
- Hover: accent outline + soft halo. Press: `translateY(1px)`.

---

## 9. Readout (pool + results)

One row. Left: `− [POOL / number] +`. Right: three stats `HITS · CRIT · FAIL` (label over number, right-aligned, 16px gaps, `min-width:2ch`).
- Pool number: `--ink`.
- Hits: `--brass` + accent glow. Crit: `--gold` + `0 0 12px var(--gold-glow)`. Fail: `--danger` + `0 0 12px var(--red-glow)`.
- Idle (before a roll): all show `--` in `--muted` at 60% opacity, no glow.
- On every result change the numbers run `tick` (a 0.4s `steps(5)` opacity stutter).
- Fixed row heights so nothing shifts between idle, rolling and result.
- Large landscape: hairlines above and below the readout (`border-block:1px solid rgba(var(--rgb),.12)`).

---

## 10. Control bar (below the tray)

Top to bottom:
1. **Crit row**: `[🔒 LOCK CRITS OFF] [+ DUPE CRITS] [↶ UNDO]` (gold except Undo).
2. **Action row**: `SORT · RE-ROLL 1S · RE-ROLL FAILS · REMOVE FAILS`.
3. **Hint**: `> TAP A TARGET TO ROLL · HITS ON IT OR HIGHER` (hidden on very short landscape).
4. **Target keys**: six columns `1+ … 6+`, 8px gaps.
5. **Crit threshold**: a `CRIT` label under `1+`, then checkboxes under `2+`–`6+`, aligned in the same 6-column grid (`margin-top:-4px`).

Portrait spare height (e.g. Home Screen mode with no browser bars): JS sets `--grow` (0–16px) on `.app`, which is added to button heights, key size and bar gap so the controls fill the screen instead of leaving a gap.

---

## 11. Menu, roll log and guide

### Menu (dropdown under the Menu button)
`.menu.frame`, right-aligned, `min-width:270px`, padding `4px 6px`, bracket 8px. Rows separated by **1px dashed `--phos-dim`** rules:
- `ROLL LOG  <count>` and `GUIDE  ?` — `.m-item`, 12px 8px padding, value in `--brass`, hover `--brass-soft` fill + accent text.
- `THEME` + six 30×30 swatches: fill `var(--c)`, 1px `rgba(255,255,255,.12)` border, `inset 0 0 0 3px var(--slab)` (a slab ring inside); selected → border in its colour + `0 0 10px var(--c)`; hover `scale(1.08)`.
- `DICE POOL` + segmented `S | M | L`: 48×44 buttons, 13px bold, 1px `--phos-dim` dividers; selected = solid accent fill, `--phos-ink` text.
Entry animation `menuIn` 0.22s (fade with a dip, 6px slide).

### Roll log (bottom sheet)
`.sheet.frame`, bottom-anchored, `width:min(100%,520px)`, max-height 75%, bracket 16px, no bottom border. Scrim: `rgba(1,6,4,.8)` + `blur(2px)`.
- Header: `ROLL LOG · THIS SESSION` (11px, accent) + `CLEAR` (two-tap confirm) + `CLOSE`, dashed rule below.
- Row grid: `14px 40px 1fr auto`, 10px gap, 10px 16px padding, dashed separators.
  - Timeline: a vertical 1px `--phos-dim` line at x=22px joining **diamond dots** (8px square rotated 45°, 2px border); the current row's dot is solid accent with glow.
  - Big hits number (22px accent glow).
  - Title: `ROLLED · 24D6 · 4+ · 3 CRIT · 5 FAIL`, then a strip of 7×7 squares per die: miss `--phos-dim` 60%, hit `--brass`, crit `--seal-hi` + glow, 1 `--danger`.
  - Time `HH:MM:SS` (24h, muted); current row shows `NOW` in accent with `--brass-soft` background.
- Empty: `> No rolls logged. …` centred, muted.

### Guide (modal)
`.guide.frame`, centred, `width:min(calc(100% - 32px),420px)`, bracket 16px. Header: `// GUIDE · 1 OF 3` + 17px heading + 32×32 `✕`. A 160px dashed-border "slide" panel (`rgba(var(--rgb),.03)` fill) holding small animated diagrams; body text; citation; progress dashes (18×4px, active 28px accent + glow); `DON'T SHOW ON LAUNCH` checkbox; nav `[ SKIP ]` / `[ BACK ]` and a primary `[ NEXT ]` / `[ START ROLLING ]`.
Slide diagrams use the same vocabulary: a typed title with a block cursor; a row of flickering squares; bar charts with muted (real-dice) vs accent (app) bars, a dashed "fair" line and value labels; a wobbling grey die `≈` a pulsing striped "chip".

---

## 12. Motion catalogue

Character: **stepped / flickering, short, bright** — CRT signal behaviour. Use `steps(1)` for glitches, brightness spikes via `filter:brightness()`, and opacity "dips" (fade in, dip back down, then settle). Nothing eases in softly except the crit pop overshoot.

| Name | Trigger | Spec |
|---|---|---|
| `mat` (materialise) | Die added to the pool | 0.45s ease-out, staggered `--dd` (22ms per die, max 300ms; 6ms on L). Starts `scale(1.4) blur(4px) brightness(2.5)` → dips to opacity .3 at 45% → overshoots to `scale(.96)` at 60% → settles. |
| `glitchRoll` | While rolling (≈620ms; faces shuffle every 60ms) | 0.5s `steps(1)` infinite, random negative delay per die. ±1px horizontal jumps, a slice `clip-path:inset(30% 0 22% 0)`, brightness 1.0–1.4, and an RGB split `box-shadow:-1px 0 0 rgba(224,70,82,.3), 1px 0 0 rgba(var(--rgb),.35)`. Deliberately gentle. |
| `land` | Normal die lands | 0.35s ease-out from `scale(1.12) brightness(2.2)`. |
| `glitch` | A 1 lands | 0.36s `steps(1)` shake (±3px, brightness 2 → 1.4). |
| `pop` | A crit lands | 0.45s `cubic-bezier(.3,1.6,.5,1)`, `scale(1.28)` at 40%, starting `brightness(2.4)`; crits stagger 40ms each (max 400ms). |
| `glow` + `pulse` | Crit halo | glow 0.9s (0 → 1 → .35), then pulse 2s ease-in-out infinite (.3 ↔ .95). |
| Spark burst | A crit lands | 8 four-point stars (6px, `--seal-hi`, clip-path star) fly outward at 45° steps ±10°, distance 0.7–1.2 × 85% of cell width, 0.7s `fly`. |
| Twinkle | Crits at rest | 3 stars at the corners (30%, 22%, 18% of the cell), `twinkle` 1.8s infinite, random delays: scale .2 → 1 with a 45° turn → fade. |
| `keyBob` | Target keys, always | 3.6s cycle: still until 56%, rise to −6px by 64% (ease-out), hold to 78%, drop fast to +1px at 84% (overshoot), settle at 90%. |
| `tick` | Result numbers change | 0.4s `steps(5)` opacity: .15 → 1 → .45 → 1 → .7 → 1. |
| `blink` | Title cursor | 1.1s `steps(1)`, off at 50%. |
| `sweep` | Background scan band | 7s linear infinite. |
| `menuIn` / `sheetIn` / `guideIn` | Opening overlays | 0.22–0.3s: opacity 0 → .6 → .25–.3 dip → 1, with a small translate. |
| CRT flicker | Random every **45–75s**; also on every action-row click (2× speed) | 0.7s (0.35s fast): `.crt-band` rolls down the screen (accent band with an `--rgb-hi` core), scanlines thicken to `rgba(0,0,0,.5) 0 2px / 4px` and jump `background-position` in steps, and `.app` gets `crtFlicker` (brightness 1.5/.55/1.3/.75… with ±2px x-jitter). Skipped when the tab is hidden. |
| Cable spark reflection | First 3–12s after load, then every **1–2 min**; tap the title to trigger | A sparking cable "in front of" the screen, reflected in the top-right of the glass (x 72–92%, y 6–22%). Overall opacity .34. Cool white/blue palette (`rgba(225,240,255)` → `rgba(90,160,255)`), heavy blur (glow 14px, core 8px), a faint full-screen wash. 11–16 irregular on/off steps of 40–130ms. 1–2 warm sparks (`rgba(255,240,210,.7)`, blur 3px) pop up 20–80px, then fall 60–200px over 0.6–1.3s. **No screen shake.** |

**Reduced motion** (`prefers-reduced-motion:reduce`): sweep, cursor, spin, pop, materialise, land, tick, key bob, overlay entrances, flicker and cable sparks are all off; sparks and twinkles are hidden; the crit halo sits steady at 80%. The colour states still communicate everything.

**L tray lite mode** (12×12, up to 144 dice): one glitch on the whole tray (`trayGlitch`) instead of per die, a quarter of the faces shuffle every 120ms, flat dice (single inset outline, no bevel or pip glow), steady crit halos (no pulse or twinkle, `0 0 6px 1px` at .45), no landing flashes, a blur-free `matLite` entrance, and at most 6 crit bursts per roll.

---

## 13. Layout

### Portrait (default)
`.app`: single column, `max-width:520px`, centred, `padding: 14px max(18px, safe-left+16px) 16px`, 10px gaps. Order: header (title left, Menu right) → readout → tray (`.stage`, flex:1, tray centred and square) → control bar. The safe-area top/bottom insets are applied once on `:root` (iOS `viewport-fit=cover`, status bar `black-translucent`).

### Landscape (aspect ≥ 13/10)
Two-column grid: tray on the left at full height (`--tray`, measured in JS from the real space), sidebar on the right `minmax(300px,500px)` with header / readout / controls; controls `align-self:center` and scroll internally if they must. 20px column gap.
- **Short landscape (≤430px tall, phones)**: compact sizes (17px title, 24px numbers, 28/32px buttons, 36px keys, 18px checkboxes, hint hidden).
- **≤300px tall**: labels hidden, 30px keys, 26px buttons.
- **≤640px wide**: Lock crits state word hidden.

### Large landscape (≥1000×600)
Breathing room and scale-up, driven by `--u = clamp(0px, (100vh - 600px)*.04, 16px)`:
- Padding `--pad` as in §5; column gap `clamp(32px,4.5vw,72px)`; sidebar `minmax(var(--side-min), min(600px,40vw))` with `--side-min` 360px (420px at ≥1100px wide).
- Buttons `46px + u` (action) / `42px + u*.75` (crit); keys `58px + u*1.5`; checkboxes `24px + u*.25`; step buttons `40px + u*.5`; numbers `40px + u`; title `24px + u*.5`; Menu button `38px + u*.5`.
- Controls stay one centred group (do not spread them evenly — the crit checkboxes must stay directly under their dice).
- SVG CRT tube (§5).

---

## 14. App icon and PWA

- Icon: 512 canvas, radial `#151a21` → `#05070a` background; a rounded die (`rx 58` on 344px) filled with a diagonal blood gradient `#6a1c25` → `#3a0e15` → `#16050a`, 7px `#e04652` stroke plus a blurred glow copy at 55%; a soft highlight arc `#ff8a92` at 35%; the skull path in `#f06a74` with a soft glow; and a scanline pattern over everything (1.4px black lines every 4px at 22%).
- Manifest: `display: standalone`, background/theme `#05070a`. `apple-mobile-web-app-status-bar-style: black-translucent`.
- Double-tap zoom is prevented (`touch-action:manipulation` plus a JS fallback).

---

## 15. Voice and copy

- Short, uppercase, terminal-like: `ROLL LOG`, `DICE POOL`, `RE-ROLL FAILS`, `START ROLLING`.
- Prefixes: `// ` for labels, `> ` for hints and empty states, `[ ]` around guide nav.
- Separator: ` · ` (middle dot with spaces).
- Numbers in tabular figures. Use `--` as the empty/idle value.
- Avoid game-brand references (it's a generic tabletop wargame roller).

---

## 16. Checklist when adding something new

1. Colours come only from tokens / `rgba(var(--rgb),…)`; gold and red only for crit and fail meaning. Test all six themes.
2. Square corners on controls; panels use `.frame`; 1px `--phos-dim` borders that go to `--brass` + soft glow on hover.
3. Monospace, uppercase, tracked labels, with `//` or `>` prefixes where it reads as terminal output.
4. Motion is stepped and brief (≤0.7s), with brightness flashes or opacity dips — and it is disabled under reduced motion.
5. Overlays are `pointer-events:none` and sit under the CRT glass (z < 10000) unless they are modal UI.
6. It fits the one-screen layout at 360×640 portrait, 844×390 landscape and 1440×900 desktop, with no page scroll.
7. On the L tray, keep per-die effects cheap (no per-die filters or infinite animations).
