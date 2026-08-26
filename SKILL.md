---
name: refactoring-ui
description: Design and improve user interfaces using the concrete rules from Refactoring UI (Wathan & Schoger) — constrained spacing/type/color/shadow scales, visual hierarchy through weight and color rather than size, and depth through emulated light. Use when building or styling any UI (web, app, dashboard, landing page, component), when picking font sizes, spacing, colors, shadows or border radius, when designing a color palette or design tokens, and whenever someone says a UI "looks off", "looks amateur", "feels cluttered/plain/unfinished", or asks to "make this look better".
---

# Refactoring UI

Visual design is not talent. It is a small set of systems decisions made **once**, plus a
handful of techniques for creating hierarchy. This skill is those systems and those
techniques.

The single biggest cause of amateur-looking UI is picking values ad hoc — 17px here,
`#3B82F6` there, `lighten(5%)` for a hover state. Design *from a scale*, always.

---

## The systems — use these, do not re-derive them

Pick from these lists. Never invent a value that isn't on one.

### Spacing and sizing

Base 16px, built from factors and multiples of it:

```
4  8  12  16  24  32  48  64  96  128  192  256  384  512  640  768
```

Tight at the small end, spreading out at the large end. **No two adjacent values may be
closer than ~25%** — that is what makes the choice obvious. A linear "multiples of 4"
scale fails: it does not help you decide between 120px and 124px.

Use it for margin, padding, width, height, icon sizes, border width — everything spatial.
Fix a small set of opacity values too (e.g. `.05  .1  .2  .4  .6  .8`) for disabled states,
overlays and hover tints, rather than eyeballing a slider each time. Same logic as the other
scales: decide once, reuse everywhere.

### Type scale

```
12  14  16  18  20  24  30  36  48  60  72
```

Not a modular scale built from a ratio (4:5, 2:3, golden ratio). Those produce fractional
pixel values that round inconsistently across browsers and are too sparse for interface
density — hand-picked values, chosen for how they feel, win instead.

**Units: `px` or `rem` only. Never `em`.** `em` is relative to the current font size, so a
`.875em` inside a `1.25em` parent computes to 17.5px — a value not in your scale. The
scale silently stops existing.

### Font weight

Two weights is enough:

- **400 or 500** — body and most UI text
- **600 or 700** — anything emphasized

Nothing below 400 in UI. To de-emphasize, use a lighter *color* or smaller *size* — never a
lighter weight.

### Color

You need far more colors than a five-swatch palette generator gives you.

- **Greys: 8–10 shades.** Almost all of a UI is grey — text, backgrounds, panels, borders,
  form controls. Three or four shades always runs out. Start at a very dark grey, not true
  black (true black looks unnatural).
- **Primary: 5–10 shades**, one or maybe two primaries.
- **Accents: 5–10 shades each** — destructive red, warning yellow, positive green, plus
  whatever else the product needs to distinguish (chart series, calendar events, tags). Ten
  colors × 5–10 shades is normal for a complex UI.

Name them `100` (lightest) → `900` (darkest), base `500`.

**Build the scale in this order:** pick `500` first (for a primary/accent, it should be a
shade that works as a button background). Then find the edges — `900` is usually your text
color, `100` a background tint; an alert component uses both, so design one and read the
two values off it. Then fill `700` and `300` as the perfect compromise between their
neighbours, then `800 600 400 200` the same way.

**Write colors as HSL, not hex.** `hsl(220, 95%, 34%)` and `hsl(220, 65%, 61%)` are
visibly related; `#03369E` and `#507DD7` are not.

**Never generate shades at runtime** with `lighten()` / `darken()`. That is how you end up
with 35 slightly different blues.

### Shadows — five elevations

```css
0 1px 3px  hsla(0,0%,0%,.2)   /* barely raised — buttons */
0 4px 6px  hsla(0,0%,0%,.2)   /* dropdowns */
0 5px 15px hsla(0,0%,0%,.2)
0 10px 24px hsla(0,0%,0%,.2)
0 15px 35px hsla(0,0%,0%,.2)  /* modals */
```

Choose by asking *where on the z-axis does this sit?*, not *what shadow looks nice?*.
Closer to the user = more attention. Shrinking a button's shadow on `:active` makes it feel
pressed; growing a list item's shadow when it's picked up for drag-to-reorder does the
reverse — it reads as "now above its siblings" and doubles as the drag affordance itself.

These five are the default and are fine everywhere. `references/techniques.md` gives a
refined **two-part** version of the same scale — parallel, but not identical values (its top
step is heavier). Use it when shadows are prominent in the design; use these when they
aren't. Don't mix the two in one project.

### Line-height and line length

Line-height is **inversely** proportional to font size, and proportional to line width:

- small text / wide columns → `1.5` to `2`
- large headlines → `1` is fine

Line length: **45–75 characters**, i.e. `max-width: 20em–35em`. This applies to the
paragraph even when the container around it is wider — mixed widths in one content area
look more polished, not less.

(`em` is correct *here* — measure should scale with the text it wraps. The "never `em`" rule
is scoped to the *type scale*, where `em` compounds through nesting. Don't "fix" this.)

### Border radius

Pick one and stay consistent. Small radius = neutral. Large radius = playful. None =
serious/formal. Mixing square and rounded corners in one interface always looks worse.

---

## The procedure

**1. Start with a feature, not a layout.** Don't design "the app" — you cannot decide
between top nav and sidebar before you know what's in the product. Design one real piece of
functionality (the search form, the message composer), and let the shell emerge.

**2. Detail comes later.** Ignore typefaces, shadows and icons early. Work in **grayscale
first** — it forces hierarchy to come from spacing, contrast and size rather than color.
Add color once the layout works.

**3. Don't over-invest in low fidelity.** Sketches, wireframes and mockups are disposable —
nobody can use a static picture of an app. They exist to explore ideas; abandon them once the
decision is made and go build the real thing.

**4. Design the smallest useful version, then build it.** Work in short design→code cycles.
Don't imply functionality you aren't ready to build — a comment box with an attachments
zone you can't ship yet blocks the whole feature. Nice-to-haves get designed later.

**5. Choose by elimination.** When picking a value from a scale: guess the one you think is
right, then compare it against the neighbour on each side. Two will be obviously wrong. If
an outer option wins, re-run the comparison with that as the new middle.

**6. Start with too much white space and remove it.** Adding space until something stops
looking bad gives you the minimum. Starting generous and trimming gives you the right
amount. Dense UIs (dashboards) are legitimate — but as a deliberate decision, not a default.

**7. Shrink the canvas.** A small component designed on a 1400px artboard tends to sprawl,
because the space is there to fill. Start at ~400px and design the mobile layout first, where
the constraints are real — then bring it to a large screen and relax only what genuinely felt
cramped. You will change less than you expect.

---

## Hierarchy — the technique that does the most work

Everything on screen sits in a pyramid: primary, secondary, tertiary. When everything
competes, the UI reads as noise. This is what makes a design look "designed" — not styling.

**Size isn't everything.** Leaning on font size alone gives you primary content that's too
big and secondary content that's too small. Use **weight** and **color** to carry emphasis
instead, and keep sizes reasonable.

**Three text colors, maximum:**
- dark — primary content
- grey — secondary content
- lighter grey — tertiary (footnotes, copyright)

All three carry real body-size text, so all three need 4.5:1. "Lighter grey" means the
lightest shade that still clears it — roughly the middle of a 9-step ramp, not the pale end.
The pale shades are for disabled states and large text only.

**Emphasize by de-emphasizing.** When the important element won't stand out and there's
nothing left to add to it, soften what competes with it instead. Fade the inactive nav
items; drop the sidebar's background color so the main content sits forward.

**Actions:** style by hierarchy, not by semantics.
- Primary → solid, high contrast. Usually exactly one per page.
- Secondary → outline, or a low-contrast background.
- Tertiary → styled like a link.

Destructive ≠ big red button. If "Delete" isn't the primary action on the page, give it
tertiary treatment — then make it a big red primary button *inside the confirmation dialog*,
where it genuinely is the primary action.

**Labels are a last resort.** `label: value` gives every piece of data equal weight. Most
data identifies itself by format (`$19.99`, an email address) or by context. Where a label
is genuinely needed, fold it into the value ("12 left in stock", not "In stock: 12"), or
add it as visibly *secondary* content. Exception: on spec-sheet-style pages where users scan
*for the label*, emphasize the label instead.

**Balance weight against contrast.** Solid icons are visually heavy and will out-shout the
text beside them — soften their color to compensate. It works in reverse too: when a 1px
border is too subtle in a soft color but too harsh once you darken it, keep the soft color
and go to 2px. Add weight to fix low contrast; reduce contrast to fix excess weight.

**Visual hierarchy ≠ document hierarchy.** Semantic markup and visual weight are separate
decisions. Section titles are usually *labels*, not headlines — an `h1` at 16px is fine, and
sometimes the title should be visually hidden entirely because the content speaks for itself.

---

## Hard rules

Deviating from these produces a specific, recognizable failure.

1. **Never grey text on a colored background.** Grey-on-white works because it *reduces
   contrast*; grey on color just looks dirty. White-at-reduced-opacity looks washed out and
   disabled, and lets patterns show through the glyphs. Hand-pick a color with the
   background's hue, adjusting saturation and lightness.
2. **Never `em` for the type scale.** `px` or `rem`.
3. **Never generate shades at runtime.** Define them up front.
4. **Never use a percentage width for something that shouldn't scale.** Sidebars get fixed
   widths; the main area flexes. Elements get a `max-width` and only shrink when the screen
   is actually smaller — a login card shouldn't be *wider* at medium screens than at large.
5. **Never scale things proportionally across breakpoints.** Large elements must shrink
   *faster* than small ones. A 2.5em headline sitting on 14px mobile body copy computes to
   35px — far too big; it wants to be 20–24px there. Likewise a button's padding should get
   proportionally tighter as the button shrinks, not scale with its font size.
6. **Always more space around a group than within it.** This is the fix for "which label
   belongs to which field", cramped bullet lists, and headings that look attached to the
   wrong paragraph. Ambiguous spacing is a functional bug, not just an ugly one.
7. **Never use color as the only signal.** Add an icon, a shape, or a text cue. For charts,
   distinguish series by *contrast* (light→dark shades of one color) rather than by hue —
   colorblind users read lightness reliably, hue not so much.
8. **Contrast minimums:** 4.5:1 for normal text. The 3:1 allowance applies only to *large*
   text, which WCAG defines as **24px regular or 18.66px bold** — not 18px. Assume 4.5:1
   unless the text is genuinely that large. When white-on-color fails, flip it: dark colored
   text on a light colored tint (see `references/systems.md`). Separately, **3:1 applies to
   non-text too** (WCAG 1.4.11): if a border is the only thing identifying a control — an
   input outline, a checkbox edge — it needs 3:1 against its background. A hairline that
   merely divides content does not. Those are two different tokens, not one.
9. **Never scale an icon far from its intended size.** A 16–24px icon at 48px looks chunky
   and detail-starved. Put it inside a colored circle instead.

---

## References

**`assets/tokens.css`** — a complete, contrast-verified starting set of all the above as CSS
custom properties: spacing, type, weights, border width, a fixed opacity scale, a 9-shade
cool grey ramp, a 9-shade primary, three accent trios, five elevations, a **semantic role
layer** (`--surface`, `--text-primary`, `--action`…) and a **dark-mode block** that overrides
only those roles. Copy it in and retune
the hues rather than re-deriving the scales from this prose. Reference the roles in
components, not the raw ramps — that is what makes the dark mode work. Every text/surface
pair is verified ≥4.5:1 in both modes and every functional border ≥3:1; the deliberate
sub-threshold shades (disabled, decorative dividers, the large-text-only step) are exempt by
criterion and commented inline.

Load these when the work calls for them:

- **`references/systems.md`** — building a palette from scratch: choosing the base color,
  keeping saturation alive at the light and dark ends, hue rotation, warm/cool greys, and
  the two escape hatches for hitting contrast ratios without ugly color.
- **`references/diagnose.md`** — symptom → fix table. Load first whenever the task is
  *improving existing UI* rather than building new.
- **`references/techniques.md`** — depth and light simulation, two-part shadows, baseline
  alignment, letter-spacing, breaking out of default component shapes, and handling
  user-uploaded images.
