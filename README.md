# plan9port patches

A collection of patches against [plan9port](https://github.com/9fans/plan9port),
built and verified in the lab before landing here. Each patch applies from
the root of a plan9port checkout with `git apply`.

## Patches

| Patch | What it does |
| ----- | ------------ |
| [patches/acme-theme-bigger.patch](patches/acme-theme-bigger.patch) | `Theme`, `Bigger`, and `Smaller` commands for acme, plus per-level tag colors |

## acme-theme-bigger.patch

Ports wily's themes and Bigger/Smaller font sizing to plan9port acme
(`src/cmd/acme` only — 7 files).

New commands, typed in any tag and middle-clicked (B2), like `Font`:

- `Theme [name]` — no argument cycles through the palettes, a name applies
  it, an unknown name warns. All 11 wily palettes plus aliases, verbatim
  (default, tokyo, tokyo-light, catppuccin, catppuccin-light, solarized,
  solarized-light, nord, nord-light, rose-pine, rose-pine-dawn).
- `Bigger [n]` / `Smaller [n]` — body font only, following Font's
  precedent. `n` defaults to 1 and clamps to 1–16. Steps an effective-height
  ladder: same-scale file sizes first, then one `N*` HiDPI scale level
  (the default ladders are short: lucsans/euro only go to 8, lucm/unicode
  only has 9; lucsans/unicode has 6, 7, 8, 10, 13).

Tag colors, derived from the theme's foreground/background pair:

- Body: the base theme colors.
- Window tags: lifted toward the text color by 1/8.
- Column tags: lifted by 1/4.
- Row tag: lifted by 1/2.
- The little tag buttons follow the window tags.

Classic colors are untouched until you pick a theme. A chosen theme persists
as a `T <name>` line in the dump, placed after the column tags so an
unpatched acme skips it harmlessly.

### Apply and build

From the root of your plan9port checkout:

```
git apply patches/acme-theme-bigger.patch
cd src/cmd/acme && mk install
```

Then quit acme completely and start it again — a running acme keeps the
old code, and `mk` alone only builds `./o.acme` without installing it.

### Notes

- Verified live under Xvfb: tokyo, tokyo-light, and nord repaint every
  surface; Bigger on the default font doubles it via the scale step;
  Bigger on lucsans 8 steps to the .10 file; Smaller round-trips both;
  Dump writes the `T` line and Load restores theme, windows, and dirty
  buffer content.
- Stock quirks, unchanged by the patch: empty-column space is always
  white; a Font change to a same-height font doesn't repaint (the font
  does change); a Dump driven through the 9P event file skips its own
  window, so interactive Dump is the way to dump windows.
