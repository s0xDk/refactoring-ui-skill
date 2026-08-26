# Techniques

## Emulating a light source

Raised and inset are the same trick: decide the element's **profile**, then mimic how light
would hit that shape. Light comes from above, and people look slightly *down* at their
screens — so you see the top edge of a raised element and the bottom edge of an inset one.

Each element needs **both** effects — the lit edge and the blocked light. They go in a
single comma-separated `box-shadow`. Two `box-shadow` declarations on one selector do not
combine; the second silently discards the first.

**Raised** (button, card) — lit top edge, shadow cast below:

```css
box-shadow:
  inset 0 1px 0 hsl(224, 84%, 74%),   /* top edge, angled toward the light */
  0 1px 3px hsla(0, 0%, 0%, .2);      /* light blocked beneath the element */
```

**Inset** (well, text input, checkbox) — lit bottom lip, shadow blocked at the top. The lip
must be **lighter than the element's own face**, so the values depend entirely on how dark
that face is:

```css
/* Dark surface — the face is dark, so the lip has room to read as clearly lit. */
box-shadow:
  inset 0 -2px 0 hsl(211, 30%, 34%),    /* bottom lip, on a --grey-800 face */
  inset 0 2px 2px hsla(0, 0%, 0%, .25); /* light blocked by the lip above */

/* Light surface — the face is already near-white, so there is almost no room to go
   lighter. Let the top shadow do the work and keep the lip to a hairline, or skip it. */
box-shadow:
  inset 0 -1px 0 hsl(0, 0%, 100%),      /* on a --grey-100 face */
  inset 0 2px 2px hsla(0, 0%, 0%, .06);
```

Copying dark-surface values onto a white input paints a dark line along the bottom, which
reads as a stray border — the opposite of the intended cue. Always check the lip against the
face.

Note both lit edges are `inset` — a non-inset shadow with a negative Y offset draws *above*
the element, not on its bottom lip.

Two rules: **hand-pick the lighter color** rather than overlaying semi-transparent white —
white overlays drain the saturation out of the underlying color, which is why both examples
above use a solid `hsl()` sampled from the element's own hue. (On a neutral grey or near-
black surface there is no saturation to lose, so `hsla(0,0%,100%,.15)` is fine there.) And
**keep blur radii tiny** — these edges are sharp in the real world, like the shadow under a
wall outlet.

Don't chase photorealism. Borrow the cue and stop.

## Two-part shadows

Good shadows are usually two shadows doing two different jobs:

- **Cast shadow** — larger, softer, bigger Y offset and blur. The shadow thrown behind the
  object by direct light.
- **Contact shadow** — tighter, small offset and blur. The area *underneath* the object that
  even ambient light can't reach. This is what keeps the element's edges defined.

```css
box-shadow:
  0 10px 20px hsla(0, 0%, 0%, .15),  /* cast    — larger, softer */
  0 3px 6px   hsla(0, 0%, 0%, .10);  /* contact — tighter, sharper */
```

The two parts must differ *substantially* in offset and blur or the effect is invisible —
that's a ~3x difference in both here.

**Which one is darker depends on elevation, and this is the whole point.** At rest on the
surface the contact shadow is the darker of the two (`.24` against the cast shadow's `.12`);
as the object lifts, it fades out and ends up lighter, until at the top of the scale it's
gone entirely. Don't fix the alphas — let them cross over. Keep both inside `.05–.25`;
anything heavier reads as a smudge rather than a shadow.

The tradeoff: at the lowest elevations the two shadows converge in geometry (`0 1px 3px` +
`0 1px 2px`) and the technique buys you little beyond a slightly crisper edge.

**As elevation increases, the tight dark shadow fades.** Lift an object off your desk and the
dark contact shadow disappears first. So an elevation scale looks like:

```css
/* lowest  */ 0 1px 3px hsla(0,0%,0%,.12), 0 1px 2px hsla(0,0%,0%,.24);
/*         */ 0 3px 6px hsla(0,0%,0%,.15), 0 2px 4px hsla(0,0%,0%,.12);
/*         */ 0 10px 20px hsla(0,0%,0%,.15), 0 3px 6px hsla(0,0%,0%,.10);
/*         */ 0 15px 25px hsla(0,0%,0%,.15), 0 5px 10px hsla(0,0%,0%,.05);
/* highest */ 0 20px 40px hsla(0,0%,0%,.2);
```

Distinct at the lowest elevation, gone entirely at the highest.

## Depth without shadows

Flat design still conveys depth — it just uses different cues.

- **Color.** Lighter feels closer, darker feels further away. A white card on a grey page pops
  forward; a grey well on a white page recedes.
- **Solid shadows.** A short vertical offset with **zero blur** — `0 3px 0 hsl(220,7%,83%)` —
  lifts a card off the page without breaking the flat aesthetic.
- **Overlap.** The strongest cue of all. Offset a card so it straddles the boundary between
  two background sections (`margin-bottom: -60px`), or make an element taller than its parent
  so it breaks out on both sides (`margin: -60px 0`). Works at component scale too: pull
  carousel arrows outside the slide with negative margins.

When overlapping images, give them a border in the **page background color** — an invisible
border that guarantees a visual gap so the images never clash.

## Baseline, not center

When two different font sizes sit on the same line — a card title and its "See all" action —
vertical centering offsets their baselines and looks subtly wrong, especially when the sizes
are close. Align to the baseline your eye is already reading from:

```css
align-items: baseline;
```

## Letter-spacing

Default to trusting the type designer. Two exceptions:

- **Headline use of a body typeface.** Faces built for legibility at small sizes (Open Sans)
  have wider tracking than faces built for headlines (Oswald). Tighten by about `-0.05em` to
  get that condensed headline feel. Don't try the reverse — a headline face doesn't become
  legible at small sizes just because you loosened it.
- **All-caps.** Lowercase letters vary in shape (ascenders, descenders, x-height) which is
  what makes them scannable. Caps are uniform blocks, so default tracking crowds them. Add
  about `+0.05em`.

## Choosing typefaces without taste

- Neutral sans-serif is the safe default. The system font stack is a legitimate choice:
  `-apple-system, Segoe UI, Roboto, Noto Sans, Ubuntu, Cantarell, Helvetica Neue`.
- **Ignore families with fewer than five weights.** Filtering Google Fonts to 10+ styles cuts
  ~85% of the options and what remains skews toward carefully-made families.
- Optimize for legibility: taller x-height, wider default tracking. Avoid condensed faces
  with short x-heights for UI text.
- Sort by popularity — a widely used font is usually a good font. And inspect sites you admire.

## Personality is four decisions

Not a vibe — four concrete levers:

1. **Typeface.** Serif → elegant/classic. Rounded sans → playful. Neutral sans → plain, lets
   other elements carry the personality.
2. **Color.** Blue is safe and nobody objects. Gold reads expensive. Pink reads fun.
3. **Border radius.** Small = neutral, large = playful, none = formal. Be consistent.
4. **Language.** "Thank you Mr. Benson" vs "Sweet, thanks Steve!" changes the product's
   character more than any color choice.

If you can't decide, look at the other sites your users spend time in. Don't imitate direct
competitors — you'll look like a lesser version of them.

## Grids are overrated

A grid is just fluid percentage widths chosen from a constrained set. That's the wrong tool
whenever an element has an optimal *fixed* size:

- **Sidebars** should be a fixed width sized to their contents; the main area flexes and runs
  its own internal grid.
- **Cards and forms** get a `max-width` and only shrink when the viewport is actually smaller.
  Sizing a login card as "6 columns, then 8 columns at medium" produces the absurd result of
  the card being *wider* on medium screens than on large ones.
- Inside components, don't use a percentage unless you genuinely want the thing to scale.

Don't compromise a component's size until the screen actually forces you to.

**Think in columns, not width.** When a component wants to stay narrow (a form field) but
sits in a wide layout, don't stretch it to fill the space — split the supporting content
into its own column instead (a hint or error message beside the input, not wrapped under
it). Solves the "feels unbalanced" complaint without breaking the component's own sizing.

## Break the default component shape

Most components look generic because of an inherited mental picture, not a constraint.

- **Dropdown** — it's just a floating box. Give it sections, multiple columns, icons,
  descriptions under each item, a "NEW" badge.
- **Table** — columns don't have to hold one field each. Merge a non-sortable column into a
  related one (name over role, amount over policy type) to create hierarchy. Add avatars,
  colored status pills.
- **Radio group** — if the choice is central to the page, make them selectable cards showing
  the actual differences, not a stack of circles.

Constraints are powerful, but a design occasionally needs freedom from an assumption nobody
checked.

## Working with images

**Text over photos.** The problem is the image, not the text — photos have bright and dark
regions, so no single text color works everywhere. Reduce the image's dynamics:
- semi-transparent overlay (`hsla(0,0%,0%,.55)` — black for light text, white for dark text)
- lower the image's contrast, raising brightness to compensate — `brightness +40%,
  contrast -70%` is a reasonable starting point, then adjust by eye
- colorize: lower contrast → desaturate → solid fill in `multiply` blend mode
- text-shadow used as a glow: large blur, no offset — `text-shadow: 0 0 50px hsla(0,0%,0%,.4)`

The last one preserves the most of the original photo.

**Everything has an intended size.** Icons drawn for 16–24px look chunky and detail-starved at
48px; icons drawn large look choppy when shrunk. If small icons are all you have, put them at
their real size inside a larger colored shape. Screenshots: capture a smaller viewport, or crop
to one region, or draw a simplified illustration — never shrink a full desktop screenshot by
70%. Favicons: redraw a simplified mark at target size rather than letting the browser
downscale the logo.

**User-uploaded content.** You can't control it, so contain it:
- Fixed-size containers with `background-size: cover`, cropping the overflow.
- Prevent background bleed (a user photo whose edges match your UI background) with a subtle
  inset shadow — `box-shadow: inset 0 0 0 1px hsla(0,0%,0%,.1)` — rather than a border. Borders
  clash with the image's own colors; nobody notices the shadow.

**Photos themselves.** Bad photography ruins an otherwise good design. Hire a photographer or
use good stock. Never design against placeholders planning to shoot something on a phone later.
