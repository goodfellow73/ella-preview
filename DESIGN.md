# DESIGN.md — Ella Patisserie (אלה בית קפה ופטיסרי)

Design system for the site rebrand. Drop this at the repo root; Claude Code reads it as the source of truth for every visual decision. If a request conflicts with this file, this file wins unless the user says otherwise.

Reference implementation: `Ella Patisserie.dc.html` (homepage, all sections built).
Full page-by-page build order + copy: `BUILD-SPEC.md`.

---

## 0. Design style — read this first

**Editorial bakery.** Heavy black Heebo headlines at near-poster scale, one cocoa ink, one cream ground, photography in **plain squares and rectangles** — the crop never decorates. The page should feel like a printed bakery broadsheet that happens to sell online — calm, confident, warm, a little old-world.

Non-negotiables:

- **Two backgrounds only** per page — cream and sand — plus at most one full cocoa-ink block.
- **Cards get a 1px border, never a shadow.** Only two shadows exist in the whole system (§4).
- **Gradients exist only as photo scrims** — two of them: `--scrim-hero` (hero) and `--scrim` (photo-tile captions). Nothing else in the system gradients.
- **Headlines are weight 900 with negative tracking.** Never 600/700 for a section heading.
- **A section heading stands alone.** No kicker, eyebrow, or small label above it — the H2 is the first thing in the block. Break it across lines with explicit `<br>` where the meaning breaks, and let scale alone carry the hierarchy.
- **The hero H1 is the one exception, and only because the brand line lives *inside* it.** `אלה בית קפה ופטיסרי` is set in weight 300 as a `<span>` on the first line of the H1, with the 900 tagline under it — one heading, two weights. That is a change of weight within a title, not a label stacked above one; a separate `<p>` above the H1 would be the banned pattern.
- **The hero is a full-bleed photograph with the headline set over it** — not a two-column text/photo split. It is the one place the page goes edge-to-edge.
- **Photographs are not given decorative crops.** The arch crops this system used to carry are gone: the about image is a hard-cornered square, product cards are squares, tiles are gently rounded rectangles. If a photo needs a shape to be interesting, the photo is the problem.
- **Product cards lead with the product photo.** A card without a real photograph is not shippable.

Explicitly banned (these are what make a bakery site look generic):

- Script/handwriting fonts, emoji, hearts, whisk/cupcake icon sets, hand-drawn SVG doodles. **One icon exists in the whole design** — the cart basket (§6) — and it is a control affordance, not decoration. The ban is on iconography that illustrates the *food*; a glyph that tells you which button buys something is doing a different job.
- Blush-pink pastel palettes, soft-focus AI food renders, pink kitchen props.
- Icon-row "features" strips (ours is typographic — label + sub, no icons).
- Kicker / eyebrow labels above a heading (`התפריט שלנו`, `בפארק תעשיות עמק חפר`, `בחנות`) and the little gold rule that went with them.
- Aggressive gradient backgrounds, glassmorphism beyond the sticky header, rounded box with a left accent border.
- Full-width carousels, parallax, scroll-jacking, counters, badges like "100% Fresh!".
- Inter / Roboto / Poppins / Fraunces. Hebrew sites in particular: no Rubik-by-default.

---

## 1. Fonts

**Heebo** is the only family — Hebrew and Latin both. It carries the whole hierarchy through weight and scale.

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Heebo:wght@300;400;500;700;900&display=swap" rel="stylesheet">
```

```css
--font-sans: Heebo, "Helvetica Neue", Arial, sans-serif;
```

Weights in use: `300` the hero brand line, `400` body, `500` nav + hero sub-line, `700` card titles + buttons, `900` headlines + prices. `300` is reserved for that one delicate line — do not spend it on body copy. Never use `600` (Heebo has it; the system doesn't).

### Type scale

| Role | Size | Weight | Tracking | Leading |
|---|---|---|---|---|
| Hero H1 | `clamp(40px, 6.4vw, 78px)` | 900 | `-0.02em` | `0.98` |
| Section H2 | `clamp(28px, 3.6vw, 46px)` | 900 | `-0.015em` | `1.1` |
| Dark-block H2 | `clamp(28px, 4vw, 52px)` | 900 | `-0.02em` | `1.1` |
| Hero brand line (inside H1) | `clamp(27px, 3.6vw, 42px)` | 300 | `0.02em` | `1.25` |
| Hero sub-line | `clamp(19px, 2.2vw, 26px)` | 500 | — | `1.3` |
| Card title | 19–20px | 700 | — | `1.35` |
| Price | 22px (20px in hero card) | 900 | — | — |
| Body | 17px | 400 | — | `1.75–1.8` |
| Secondary / card body | 14–15px | 400 | — | `1.6–1.7` |
| Nav, buttons | 14–16px | 500 / 700 | — | — |
| Pill, badge | 12–13px | 700 | `0.04em` | — |
| Footer legal | 13px | 400 | — | — |

Rules: nothing under 13px. Body copy capped at `46–56ch`. `text-wrap: pretty` on paragraphs, `text-wrap: balance` on every heading.

**Line breaks in headings — check them at 320px, not just on a laptop.** A hard `<br>` is a decision taken at one width and it does not travel. Two failures to watch for, both of which shipped here before being caught:

1. *The stranded word.* A `<br>` plus natural wrapping gives three lines where the middle one holds two words. Fix by making the break's own line fit: the `@media (max-width:760px)` floor of `clamp(22px, 7vw, 33px)` exists precisely so `המאפים, הלחמים והסלטים` still fits its measure at 320px. Change that heading's wording and you must re-check the floor.
2. *Short line, then long line.* Balance alone will not always fix this — Chrome left `סיפורינו מתחיל בחלום / שהתגשם` at 260/116 when 185/198 was available. A hard break that reads long-then-short beats a balanced one that reads short-then-long.

So: keep the `<br>` where it also works on a phone (`about`, `cats`, the hero's three-item list), and give it `class="bp"` — hidden under 760px — where it only works on a desktop (`ראש השנה`). Measure, don't eyeball: walk the heading's text nodes with a `Range` and group the client rects by `top` to get the real lines and their widths.

---

## 2. Colors

Warm cocoa/cream/gold. ✅ **Confirmed against the live site** (2026-09-12) by reading the Elementor global kit and the compiled stylesheets at `ella-patisserie.co.il`. The five load-bearing hexes below are the real brand values, not estimates:

| Token | Hex | Where it came from |
|---|---|---|
| `--ink` | `#5C392C` | most-used color on the live site (21 declarations); also the exact color of the logo artwork |
| `--cocoa` | `#6A3C1C` | 4 declarations; action/price brown |
| `--gold` | `#D6AF42` | Elementor global custom color — the brand accent |
| `--cream` | `#FFFBF3` | 3 declarations; page ground |
| `--ink-deep` | `#503C2A` | deepest brown, used for the one dark block |

Sand, line and the muted inks are derived from those (same hue family) to fill out the system.

```css
:root {
  --cream:        #FFFBF3; /* page ground — CONFIRMED */
  --cream-card:   #FFFFFF; /* cards, sticky header, light buttons */
  --sand:         #F6EEE0; /* alternating band, soft pills */
  --sand-deep:    #EFE2CE; /* badges, light-button hover */
  --line:         #E7D9C4; /* every 1px border, every hairline */
  --photo-bg:     #EADCC6; /* image box before load */

  --cocoa:        #6A3C1C; /* PRIMARY: buttons, prices, sub-labels — CONFIRMED */
  --cocoa-dark:   #522D12; /* button hover only */
  --gold:         #D6AF42; /* brand accent — CONFIRMED */
  --gold-deep:    #B8902E; /* gold hairline / hover */

  --ink:          #5C392C; /* headlines + logo color — CONFIRMED */
  --ink-deep:     #503C2A; /* the one dark full-bleed block — CONFIRMED */
  --ink-soft:     #6B4A3A; /* long-form body */
  --ink-mute:     #755443; /* secondary body */
  --ink-faint:    #C9AA93; /* footer legal on --ink-deep only — 4.78:1 */

  --on-dark:      #FFFBF3; /* text/buttons on cocoa or ink */
  --on-dark-mute: #E6D6C4; /* body on ink */

  --scrim:      linear-gradient(to top, rgba(40,24,15,0.82), rgba(40,24,15,0));
  --scrim-hero: linear-gradient(to left, rgba(40,24,15,0.93) 0%, rgba(40,24,15,0.86) 30%,
                                rgba(40,24,15,0.66) 58%, rgba(40,24,15,0.34) 100%);
}
```

**Gold is a fill and a rule, never body text.** `--gold` on `--cream` is ~1.9:1 — it fails as text. Use it as: a filled pill/badge with `--ink` text on top (~6.2:1 ✅), a 1–2px hairline, or the nav's active underline. Gold text is permitted only on `--ink-deep`, and only at 13px/700 or larger (the hours strip is the one place it appears).

Links (set globally — CMS-added links otherwise render browser-blue):

```css
a { color: #6B3A1E; text-decoration: none; }
a:hover { color: var(--rust); }
```

Usage map: cocoa = action + price + small caps labels. Gold = accent fill, hairline and the nav's active underline, never body text. Ink = headlines, and exactly one full-bleed block per page. Sand = the one alternating band. Everything else is cream.

Contrast floors — **all measured, all pass** (don't lighten any of these):

| Pair | Ratio |
|---|---|
| `--ink` on `--cream` | 9.80 |
| `--ink-soft` on `--cream` | 7.63 |
| `--ink-mute` on `--cream` / on `--sand` | 6.55 / 5.87 |
| `--cocoa` on `--cream` / on `--sand` | 8.94 / 8.01 |
| `--on-dark` on `--cocoa` | 8.94 |
| `--on-dark-mute` on `--ink-deep` | 7.31 |
| `--ink` on `--gold` (badge) | 4.85 |
| `--gold` on `--ink-deep` (hours strip) | 4.98 |
| `--ink-faint` on `--ink-deep` (footer legal) | 4.78 |

The hero headline sits on a photograph, so its contrast comes from `--scrim-hero`, not from a token pair. Any replacement hero image must be re-checked against the scrim — if the headline lands on a bright region, deepen the scrim rather than lightening the type.

---

### 1a. Reviews: what may and may not be shown

The testimonials section pairs **selected quotes** with the **unfiltered overall score**, and that pairing is the point — it is what keeps the section honest.

- Choosing which quotes to feature is ordinary marketing, **provided the section reads as curated** (`מה לקוחות מספרים`) and not as a live feed.
- Never label a filtered set "ביקורות גוגל" or render it as if it were the full feed. Showing only 4★-and-up under Google's name misrepresents the business to the reader and runs against the Maps Platform terms, which forbid presenting the content in a way that alters its meaning.
- Whatever is curated above, the **real aggregate — score and total count — is always shown**, linked straight to the listing so anyone can read the rest.
- The Places API returns at most 5 reviews and cannot filter by rating server-side, and on a static host the API key is public. Hand-entered quotes plus a linked aggregate avoid all of that.
- **Never invent a quote, a name, or a score.** Until the real ones arrive the section ships with `.todo` placeholders — deliberately striped and monospaced so they cannot be mistaken for content or reach production unnoticed.
- **Status: built, held back.** The section and its real rating bar (4.3 from 1,904 Google reviews, linked by `cid`) are finished but kept out of the live page until three real quotes arrive. The markup is in `reviews.part.html` — local only, gitignored — and the page's `@@REVIEWS@@` slot is filled with nothing until then. Its CSS stays in the stylesheet so restoring it is a one-line change.

### 2a. Open experiment — the pale blue (NOT adopted)

`index-blue.html` trials `#B8D9E2`, a powder blue already present in the live site's stylesheet, on the two mid-page blocks that are currently `--ink-deep`: the hours strip and the ראש השנה block. It is an **alternate file, not a variant of the system** — `index.html` is unchanged and remains the reference. The whole experiment is one commented CSS block appended after the main stylesheet; deleting that block restores the original exactly.

The thing to know if it gets adopted: **this blue is light** (relative luminance 0.653), so it inverts the meaning of a "dark block". Measured on `#B8D9E2`:

| Foreground | Ratio | |
|---|---|---|
| `--ink` `#5C392C` | 6.77 | ✅ |
| `--cocoa` `#6A3C1C` | 6.18 | ✅ |
| `--ink-soft` `#6B4A3A` | 5.27 | ✅ |
| `--ink-mute` `#755443` | 4.53 | ✅ (only just) |
| `--gold` `#D6AF42` | 1.40 | ❌ |
| `--on-dark` `#FFFBF3` | 1.45 | ❌ |

So on blue, every `--on-dark` foreground flips to the brown inks and **gold cannot appear at all** — not as text, not as the eyebrow rule. `.btn-on-dark` inverts too: cocoa fill with cream text instead of cream fill with ink text. The footer stays `--ink-deep` on purpose; if the strip, the block *and* the footer all went blue the page would have no dark anchor left.

## 3. Language & direction (Hebrew)

- `<html lang="he" dir="rtl">`. Layout is mirrored; logical properties everywhere (`margin-inline-start`, `padding-inline`, `inset-inline`) — no `left`/`right`.
- Hebrew typographic marks: `״` for quotes, `׳` for geresh (`א׳–ו׳`, `בראנץ׳`, `קפיצה קטנה לחו״ל`). Never ASCII `"` or `'`.
- Prices `₪165.00` — symbol first, two decimals. Wrap in `<span dir="ltr">` if a CMS reverses it.
- Hours string, verbatim: `ימים א׳–ו׳ · 08:00–15:00` / `שבת סגור`.
- Hebrew `alt` on every image.

---

## 4. Shape, shadow, motion

```css
--r-pill:  999px;  /* every button, pill, badge, count bubble */
--r-card:  22px;   /* product cards */
--r-tile:  24px;   /* category tiles */
--r-panel: 28px;   /* large panels */
--r-panel-lg: 32px;

--shadow-hero: 0 30px 70px -30px rgba(59,35,23,0.45);
--shadow-float: 0 18px 40px -22px rgba(59,35,23,0.4); /* the floating price card */
```

Those two shadows are the only ones in the system. Cards, tiles, header, footer: `1px solid var(--line)`.

Motion: hover transitions ≤150ms, on `background`/`opacity`/`color` only. Tile hover is `opacity:.94` and nothing else. Two named exceptions animate `transform`:

- **The cart basket** — `220ms cubic-bezier(.34,1.42,.64,1)`, a 2px hop and a 7° tip on hover and keyboard focus, settling on `:active`. The overshoot in that curve is the whole point; a linear tween reads mechanical.
- **The phone video** — plays and pauses itself on scroll (§6).

Both are disabled under `prefers-reduced-motion`, as is the one entrance animation — the hero text column:

```css
@keyframes riseIn { from { opacity:0; transform: translateY(14px) } to { opacity:1; transform:none } }
/* animation: riseIn .7s ease both;  — hero text only, once, nowhere else */
```

No scroll-triggered reveals, no parallax, no marquees.

---

## 5. Layout

- Content width `1240px`; inline padding `24px`; section padding `80–84px` block.
- **Vertical padding does not stack.** Where a full-bleed band sits directly above a `.sec`, give the band `padding-block: … 0` and let the section's own top padding make the whole gap. The video band and `#cats` were briefly `54 + 82 = 136px` apart for exactly this reason.
- **Every** multi-column block: `grid-template-columns: repeat(auto-fit, minmax(<min>, 1fr))` + `gap`. No media queries, no fixed widths, no `white-space: nowrap` on text boxes.
- **Exactly two media queries exist**, and neither touches a content grid — the grids stay `auto-fit`. Do not add a third without a reason as concrete as these:
  1. `max-width: 900px` — the seven-item nav wraps to three rows on a phone and eats ~60% of the viewport, so it collapses behind a `תפריט` toggle (see §6). The header's primary CTA is hidden here; the hero already carries it.
  2. `max-width: 760px` — two things that are genuinely format-dependent, not just narrower: `--scrim-hero` is a *sideways* gradient with no horizontal room left on a phone, so the hero swaps to a bottom-anchored scrim and bottom-aligns its text; and the vertical video and the trust band trade places (see §6).
  3. `max-width: 560px` — at 360px the logo, the `תפריט` toggle and the cart do not fit one row, and a header that wraps to two rows eats the top of a phone screen. The cart drops to its short label and the logo to 40px, which brings the header back to a single 64px row.

**On the phone the scrim must stay off the photograph.** The bottom-anchored hero scrim runs `.88 → .74 (26%) → .40 (58%) → .12 (100%)`: heavy enough under the text, which occupies the bottom ~44%, and nearly clear across the top half so the food is actually visible. Darkening it uniformly to make type easier is the wrong trade — the photograph is the reason the hero exists.

| Block | min | gap |
|---|---|---|
| Hero (text / photo) | `320px` | `40px` |
| Panels (ראש השנה, about) | `300px` | `44–48px` |
| Category tiles | `240px` | `22px` |
| Product cards | `230px` | `20px` |
| Trust band, footer | `220px` | `18–36px` |

- Every `auto-fit` minimum is wrapped in `min()`: `minmax(min(300px,100%), 1fr)`. A bare `minmax(300px,1fr)` forces a track wider than a 320px phone's content box and silently clips it, because `body` carries `overflow-x:hidden`.
- Sibling spacing is always `gap` — never per-element margins (survives drag-reorder and deletion).
- Cards use `display:flex; flex-direction:column` with the price/CTA row on `margin-top:auto`, so ragged titles still align across a row.
- Sticky header: `rgba(251,245,236,0.92)` + `backdrop-filter: blur(10px)` + bottom hairline. Nav and the cart/CTA cluster both `flex-wrap: wrap`.

## 6. Components

- **Primary button** — cocoa fill, `--on-dark` text, pill, `16px 32px` (hero) / `11px 18px` (card), weight 700; hover `--cocoa-dark`.
- **Secondary button** — `1px solid #C9A87E`, cocoa text, pill; hover `--sand`.
- **On-dark button** — cream fill + ink text (primary) or `1px solid rgba(255,248,238,.5)` + cream text (secondary).
- **Pill / badge** — `--sand-deep` bg + cocoa text, 12–13px/700. Cocoa fill + cream text for seasonal labels.
- **Text link** — 15–16px/700 with `border-bottom: 1px solid #C9A87E; padding-bottom:3px`. This replaces "read more" buttons.
- **Photo tile** — radius `--r-tile`, `--photo-bg` behind, `object-fit:cover`, caption absolutely positioned bottom over `--scrim`, 20–26px/900 in `--on-dark`.
- **Hero** — full-bleed `<section>`, min-height `clamp(520px, 74vh, 760px)`, photo `object-fit:cover` behind `--scrim-hero` (darkest at the inline-start/right edge so the RTL text column lands on the dark side). Content is a single column capped at `640px`, aligned to the inline-start edge of the `1240px` container: H1 (light brand line + heavy tagline) → one sub-line. **No buttons in the hero** — the photograph and the name carry it, and the header's `להזמנה` pill is the standing call to action. **Optional background clip:** a `<video class="hero-img hero-vid">` sits over the photo in the identical box — `muted loop playsinline preload="none"`, poster = the photo. Its `src` is attached by script (never under `prefers-reduced-motion`), it is shown only on its first `playing` event so there is never a blank frame, and an `IntersectionObserver` pauses it off screen; if a phone refuses autoplay the photo simply stays. Same crop and same scrim as the photo at every width, phones included. A clip here should be **one continuous shot with a calm right side** — hard cuts behind the headline read as flicker, and the loop point counts as a cut. (The clip live at the time of writing, `hero-video.mp4`, is 27.8 MB with four scenes and cuts at 1.0s, 1.5s, 2.6s and 7.3s; a single-shot replacement and compression to ~3–4 MB are both still owed.).
- **Mobile nav toggle** — a pill matching the cart (`1px --line`, `--cream-card`, 14px/700) holding a 3-bar glyph + the word `תפריט`; appears only under 900px. It drives `aria-expanded` on the button and `.open` on the `<nav>`; the open panel is a full-width column of 44px-tall rows separated by `--line` hairlines, and it closes itself when a link inside is clicked. This is the only scripted component on the page.
- **Product card** — `--cream-card`, 1px `--line`, `--r-card`, `overflow:hidden`; **photo first**: `aspect-ratio:1/1` — always square, identical across every card in a row, cropping the source as needed via `object-fit:cover`, `--photo-bg` behind, full card width and flush to the top corners (no inset). Body below gets 18px padding and `gap:8px`: optional badge, title, one-line description, then the bottom row on `margin-top:auto` = price + `הוספה לסל`. Photo hover `opacity:.94` only — the card itself does not lift, scale or shadow.
- **Vertical video** — a 9:16 frame, `--r-panel-lg`, `--shadow-hero`, `--ink-deep` behind, `width: min(100%, 380px)` and centred; the `<video>` fills it with `object-fit:cover`. It and the trust band occupy the same slot in the page and **swap at 760px**: the phone gets the video and no trust band, the desktop gets the trust band and no video. A vertical clip is a phone format — full-bleed on a 375px screen it is the best thing on the page, and beside a 1240px column it is a stamp. Always `playsinline` and `muted` — those two are what make autoplay permissible on iOS and Android at all. It carries `loop`, `controls` and `preload="metadata"`, and an `IntersectionObserver` at `threshold: 0.35` starts it when it scrolls into view and pauses it the moment it leaves, so it never runs unseen. Skip the observer under `prefers-reduced-motion`; the controls still let someone start it. Note what this commits a phone to: the clip downloads as soon as it is scrolled to, so its weight matters more here than it would behind a tap. Give it a real `poster` — a frame from the clip itself, never an unrelated product shot — and an `aria-label` describing the clip.
- **Testimonial card** — `--cream-card`, 1px `--line`, `--r-card`, 26px/24px padding, `gap:14px`: a cocoa star row, the quote at 17px/1.7 in `--ink-soft`, then the attribution on `margin-top:auto` (name at 14px/700 cocoa, source beneath in `--ink-mute`). Stars are **cocoa, never gold** — `--gold` measures 2.02:1 on cream and `--gold-deep` 2.88:1, so both fail even the 3:1 floor for a graphic and would read as smudges. Cocoa is 8.94:1.
- **Rating pill** — a full-width `--r-pill` bar under the testimonials carrying the star row, the score at 26px/900 cocoa, the review count, and a text link out to the Google listing.
- **Cart** — pill in the header holding, in order: an 18px inline-SVG basket (`stroke: currentColor`, `stroke-width: 1.7`, no fill, `aria-hidden`), the label, then a cocoa count bubble. The SVG is inline so it inherits colour and costs no request; `transform-origin: 50% 70%` puts the pivot at the basket's base so the tip reads as a lift, not a spin. It carries two labels, `עגלת קניות` and a short `סל`, and swaps to the short one under 560px so the header stays on one row; the `aria-label` keeps the full name for screen readers either way. It reveals a bar under the header showing item count and `סה״כ ₪X`. Empty: `העגלה ריקה — הוסיפו מאפה טרי`. Count text pluralizes (`פריט אחד בעגלה` / `N פריטים בעגלה`).

## 7. Imagery

Natural light, cream and wood surfaces. Leave headroom at the top of the frame so a square crop never clips the subject. No dark moody stock, no pink props, no AI-looking renders. Never hand-draw food as SVG — use a striped placeholder with a monospace label until the real shot exists.

Needed at 2x: hero — one wide `16/9` or wider shot of a laid table or counter with usable negative space on the **right** (RTL text side); ראש השנה `4/3`, מגשי אירוח `16/9`, seven category crops at `4/5`, product shots at `1/1` (square — see §6), about `1/1`, square corners, no radius (place/people, **not** product — the counter, the room, the light), logo as transparent PNG + SVG.

**Video.** Shoot and keep it vertical `9:16` — it earns its place on the phone, where a landscape clip would waste the column. Ship `.mp4` (H.264 + AAC). A `.mov` holding the same streams **does** play in Chrome — tested over HTTP, `readyState` 4 and playback advancing — even though `canPlayType('video/quicktime')` returns `''`; that method is conservative, not authoritative. But the server must then send it and Firefox still refuses the container, so `.mov` is a fallback, not a target. When both are listed, put the `.mp4` `<source>` first and declare the `.mov` fallback as `type="video/mp4"` — Chrome skips a source labelled `video/quicktime` without trying it. Budget ≤4MB for an ambient autoplay loop and ≤10MB for a click-to-play clip; a 26-second phone capture straight off the camera is ~48MB (≈14.6 Mbps) and must be compressed before it goes near a page. Remuxing `.mov` → `.mp4` needs no re-encode when the codecs are already H.264/AAC. Current placeholders pull from the live WordPress uploads — see the table in `BUILD-SPEC.md`.

## 8. Accessibility

Real `<button>` for actions, real `<a href>` for navigation. Visible focus ring: `2px solid var(--cocoa)` at `2px` offset. Hit targets ≥44px on mobile. Hebrew alt text everywhere. Keep `הצהרת נגישות` in the footer. Respect `prefers-reduced-motion` by skipping `riseIn`.

## 9. Working in Claude Code

- **Never invent taxonomy or catalogue content.** Category names, product names, prices and descriptions come from the live site (the WooCommerce Store API at `/wp-json/wc/store/v1/products` and `/products/categories` is readable, and the nav on the homepage lists the shop categories). If a category or product is not there, it does not go on the page. The same goes for the short sub-labels under category tiles — either take them from the site or leave them off; do not write new ones and present them as the shop's structure.
- Read `BUILD-SPEC.md` for section order, exact Hebrew copy, product names and prices before generating any page.
- New pages (shop, category, product, about, contact, ראש השנה) compose **only** from §6 components and §2 tokens. If something needs a new component, say so and propose it — don't invent a one-off style.
- Never introduce a new color, radius, shadow, font, or gradient without updating this file in the same change.
- Changes asked as "small" stay small: edit the one value, leave layout, spacing and copy alone.
