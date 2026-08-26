# Building the color system

The scales in SKILL.md tell you *what* to define. This tells you *how to pick the values*,
and how to keep them from looking washed out or failing contrast.

## Why HSL

Hex and RGB describe colors in terms a machine cares about. HSL describes them in the terms
your eye already uses:

- **Hue** — position on the wheel, in degrees. 0° red, 120° green, 240° blue. This is what
  makes two different colors both read as "blue".
- **Saturation** — how vivid. 0% is grey (at which point hue is meaningless), 100% is intense.
- **Lightness** — 0% black, 100% white, 50% the pure hue.

Two shades of the same color share a hue in HSL and look nothing alike in hex. Design tools
mostly show HSB, browsers only understand HSL — don't confuse them. In HSB, 100% brightness
is only white when saturation is 0; HSB at S100/B100 equals HSL at S100/L50.

## Picking the base (500)

There is no formula. For a primary or accent color, pick the shade that **works as a button
background** — dark enough that white text sits on it comfortably, light enough that the
button doesn't read as black. Rules like "start at 50% lightness" don't hold; every hue
behaves differently. Use your eyes.

For greys, the base matters less. Work from the edges instead: the darkest grey is whatever
you want your darkest text to be, and the lightest is a subtle off-white background.

## Finding the edges (900 and 100)

Choose them by imagining where they'll be used. `900` is almost always a text color; `100` is
almost always a background tint. A simple alert component uses both at once — dark text on a
pale tinted panel — so design one and read both values off it.

## Filling the gaps

With `900`, `500` and `100` fixed, add `700` and `300` as the perfect compromise between the
shades on either side. That leaves four holes — `800`, `600`, `400`, `200` — filled the same
way. Nine shades is convenient because it divides cleanly.

Then adjust by eye. A systematic build gets you 90% there; expect to nudge a saturation or
push a shade lighter once you see it in use. What you must *not* do is keep adding new shades
outside the system — at that point you don't have a system.

## Keeping saturation alive

In HSL, saturation's effect weakens as lightness approaches 0% or 100%. The same S value that
looks vivid at L50 looks washed out at L90.

**So: increase saturation as lightness moves away from 50%, in both directions.** Your
lightest and darkest shades should carry *more* saturation than your base, not the same
amount. This is subtle per-swatch and very visible when the color covers a large area.

Applies to greys too — if you're using tinted greys and don't raise saturation at the ends,
your palest and darkest greys will drift back toward neutral.

## Perceived brightness and hue rotation

Every hue has an inherent perceived brightness. Yellow and blue at identical HSL lightness
look nothing alike in brightness, because the eye weights the channels unevenly:

```
perceived brightness = sqrt(0.299·r² + 0.587·g² + 0.114·b²) / 255
```

Across the wheel this gives three local maxima — **60° (yellow), 180° (cyan), 300° (magenta)**
— and three minima — **0° (red), 120° (green), 240° (blue)**.

That gives you a second way to change how light a color looks, without touching lightness and
without draining its intensity:

- **To lighten:** rotate the hue toward the nearest of 60° / 180° / 300°.
- **To darken:** rotate the hue toward the nearest of 0° / 120° / 240°.

**Cap the rotation at 20–30° total.** Beyond that it reads as a different color rather than a
lighter or darker one.

This is the fix for scales built on light hues. A yellow darkened by lightness alone goes
muddy olive-brown; a yellow darkened by rotating gradually toward orange gives you warm, rich
dark shades. Combine both approaches freely — take some brightness from hue, some from
lightness.

## Warm and cool greys

True grey is S0% — no color at all. Most greys in good UIs are saturated noticeably.

- **Cool** (blue-ish): hue ~207–210, saturation ~12–21%
- **Warm** (yellow/orange-ish): hue ~39–41, saturation ~12–21%

How far you push it is a personality decision. And remember the saturation rule above: raise S
at the light and dark ends or the extremes will look flat next to the mid-tones.

## Dark mode

Beyond the book — it predates dark mode — but the ramp rules extend to it cleanly.

**Don't invert the ramp mechanically.** Swapping `100` for `900` produces harsh, glaring UI,
because the two modes aren't symmetric:

- **Never pure black as the surface.** Use `grey-900`-ish, and build *elevation by getting
  lighter*, not darker. "Raised is lighter than the page" holds in both modes — what changes
  is that shadows barely register against a dark surface, so lightness has to carry the depth
  cue on its own. Surfaces stack upward in lightness; shadows do progressively less work.
  But adjacent steps on a grey ramp are a *thin* cue — typically under 1.3:1 — so a raised
  dark surface usually also needs a hairline border to read as raised at all. Don't just
  reach for a lighter surface: pushing it further up the ramp squeezes the text sitting on
  it, and tertiary text is the first thing to fail.
- **Desaturate your accents.** A `500` tuned to carry white text on a light page will vibrate
  against a dark one. Shift toward the `300`/`400` end and drop saturation.
- **Re-check contrast; don't assume it mirrors.** Light-on-dark at the same nominal ratio
  reads heavier, so text often wants to be a shade *dimmer* than the equivalent light-mode
  pairing, not brighter. `grey-100` on `grey-900` is usually too much; `grey-200`/`grey-300`
  is the comfortable body color.
- The two escape hatches below work in reverse too: on a dark colored panel, rotating hue
  toward cyan/magenta/yellow buys contrast without washing to white.

## Hitting contrast ratios without ugly color

WCAG wants 4.5:1 for normal text. The relaxed 3:1 threshold applies only to *large* text —
defined as **18pt (24px) regular, or 14pt (≈18.66px) bold**. 18px regular text is normal text
and needs the full 4.5:1; the book's "~18px" phrasing is looser than the spec.

Dark-on-light is easy. Color is where it gets hard, and there are two moves that solve almost
every case.

### 1. Flip the contrast

White text on a colored background needs the background to be *very* dark to reach 4.5:1 — and
a page full of dark saturated badges grabs attention that those elements don't deserve.

Instead, invert: **dark colored text on a light colored tint.** A green `800` on a green `100`
easily clears AAA, keeps the semantic color, and sits quietly in the hierarchy. This is the
default treatment for status pills, tags and badges.

### 2. Rotate the hue toward a brighter one

For colored text on a colored background — secondary text inside a dark colored panel — raising
lightness alone drives you to near-white before you hit the ratio, and then the primary and
secondary text look identical.

Use perceived brightness instead: **rotate the text's hue toward cyan, magenta or yellow.** You
gain contrast while keeping the text visibly colored and visibly secondary. A blue-violet panel
with cyan-shifted body text can clear AAA and still look like part of the panel.
