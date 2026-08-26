# Refactoring UI (Claude Code skill)

A [Claude Code](https://claude.com/claude-code) skill that teaches Claude the concrete,
mechanical rules from the book *[Refactoring UI](https://www.refactoringui.com/)* by Adam
Wathan and Steve Schoger — constrained spacing/type/color/shadow scales, visual hierarchy
through weight and color rather than size, and depth through emulated light.

Every rule and CSS value in this skill was cross-checked page-by-page against the book.
It's not a summary — it's the book's decisions, made once, ready to apply.

## What it does

Load this skill and Claude will, when styling or reviewing UI:

- Pick spacing, type sizes, weights, colors, shadows and radii **from fixed scales**
  instead of ad hoc values
- Build hierarchy through weight/color rather than piling on font-size
- Diagnose vague complaints ("looks off", "feels cheap") into specific, mechanical fixes
- Apply concrete techniques for depth, contrast, images, and breaking generic component
  shapes

It does **not** include the book itself — see [Credits](#credits).

## Repo structure

```
SKILL.md                 the skill: systems, procedure, hierarchy, hard rules
references/
  systems.md              building a color palette from scratch (HSL, saturation, hue rotation)
  diagnose.md              symptom -> fix table, for improving existing UI
  techniques.md            depth/light simulation, typefaces, grids, images
assets/
  tokens.css               a complete, contrast-verified starter token set
```

## Installation

Claude Code loads skills from a folder containing a `SKILL.md`. Clone this repo into one
of the skill directories below — **use the folder name `refactoring-ui`** so it matches
the skill's declared name.

### Personal skill (available in every project)

```sh
git clone https://github.com/<you>/refactoring-ui-skill.git ~/.claude/skills/refactoring-ui
```

### Project skill (checked into a specific repo, shared with your team)

```sh
git clone https://github.com/<you>/refactoring-ui-skill.git .claude/skills/refactoring-ui
```

(Or add it as a git submodule at that path if you want to track updates.)

Restart Claude Code (or start a new session) after installing so it picks up the new
skill.

## Usage

The skill activates automatically whenever Claude is building or styling UI, picking
font sizes/spacing/colors/shadows, designing a palette or design tokens, or when you say
a UI "looks off", "looks amateur", or "feels cluttered/plain/unfinished." You can also
invoke it explicitly:

```
/refactoring-ui make this dashboard look less amateur
```

## Credits

All design rules and reasoning are drawn from *Refactoring UI* by Adam Wathan and Steve
Schoger. This repo does not include the book's text or images — only original notes and
CSS derived from its concepts. If you find this useful, buy the book:
**https://www.refactoringui.com/**

## License

MIT for the contents of this repo (see [LICENSE](LICENSE)). Does not extend to the book
itself.
