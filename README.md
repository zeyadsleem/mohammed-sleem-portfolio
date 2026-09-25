# Mohammed Sleem — Portfolio

Static one-page portfolio for **Mohammed Sleem**, graphic designer (social media,
advertising, compositing) based in Tanta, Egypt.

No build step, no framework, no dependencies. Open `index.html` and it runs.

## Stack

| | |
|---|---|
| Markup | Semantic HTML5, `dir="rtl"` `lang="ar"` |
| Style | Plain CSS, custom properties, one stylesheet |
| Script | One line of vanilla JS (the footer year) |
| Fonts | Self-hosted WOFF2, subset to the glyphs the page uses |
| Images | WebP with `srcset`/`sizes`, explicit dimensions |

## Layout

```
index.html          markup + inline SVG icon set
style.css           tokens, layout, type scale, motion
fonts.css           @font-face for the two webfonts + metric-matched fallbacks
script.js           footer year
fonts/*.woff2       15 subsetted faces (arabic / latin / latin-ext)
*.webp              portrait + four project stills
favicon.svg
```

## Run locally

```sh
python3 -m http.server 8000
# → http://localhost:8000
```

A plain `file://` open also works, but serve it over HTTP so the font preloads
and the WOFF2 requests are not blocked by CORS.

## Design system

Tokens live at the top of `style.css`. The palette is petroleum, tangerine and
warm paper:

| Token | Value | Use |
|---|---|---|
| `--ink` | `#102e33` | dark surfaces, body text |
| `--petrol` | `#163f43` | hover states on paper |
| `--paper` | `#fafaf7` | page background |
| `--tint` / `--card` / `--card-warm` | `#e7eeea` `#edf1ed` `#f9e7dc` | nested surfaces |
| `--accent` | `#ff854e` | the one accent |
| `--rust` | `#b34321` | deep accent, the certificate card |
| `--muted` | `#526862` | secondary text on paper |
| `--on-dark` / `--on-dark-2` | `#c8d5d1` `#a4bcb5` | text on `--ink` |

Text tiers on the dark hero are deliberate: `--on-dark` for body copy (9.5:1),
`--on-dark-2` for meta rows (7.2:1), `--on-dark-bright` for the name band.

## Typography notes

**Arabic headings carry no letter-spacing.** Negative tracking breaks the cursive
joins in Arabic script, so it is applied only to the Latin display elements
(`.wordmark`, `.name-band`, `.type-strip`). Latin labels at 11–12px *do* get
positive tracking (0.04–0.10em), which is what makes them legible at that size.

Display leading is tightened relative to a Latin default: `1.45` on `.hero h1`,
`1.35` on `.contact-title`. Arabic needs more leading than Latin, but not 1.7.

## Fonts

[Alexandria](https://fonts.google.com/specimen/Alexandria) (Arabic) and
[Space Grotesk](https://fonts.google.com/specimen/Space+Grotesk) (Latin), both
under the SIL Open Font License 1.1. Self-hosted — no request leaves the origin.

Three things are deliberate here:

1. **Only the subsets the page needs** are shipped: `arabic`, `latin`,
   `latin-ext`. The other Google subsets (`vietnamese`, and the rest of
   `latin-ext`) are never downloaded.
2. **Each face is subset to the ~320 glyphs the page actually renders.** This
   took the payload from 384.6 KB to 168.7 KB.
   ⚠️ **If you add copy that uses characters outside that set, re-subset the
   fonts** — otherwise the new characters fall back to a system font. Re-run the
   subsetting pass and commit the regenerated `fonts/` and `fonts.css`.
3. **The `… Fallback` faces eliminate the font-swap layout shift.** They scale a
   metric-compatible local font (Arial / Helvetica / Liberation Sans) onto the
   webfont's em box using values measured from the real font tables:

   | Face | `size-adjust` | `ascent-override` | `descent-override` |
   |---|---|---|---|
   | Alexandria | 97.18% | 99.61% | 25.83% |
   | Space Grotesk | 92.86% | 105.96% | 31.44% |

   Combined with `font-display: swap`, a late-arriving font reflows by ~0 instead
   of pushing the layout around. `body` also sets an explicit `line-height` so
   line boxes never depend on font metrics.

## Performance

Critical path is ~114 KB, all same-origin.

- LCP portrait: 2.0 MB PNG → 102 KB WebP, with a 30 KB `640w` source
- 3 font faces used above the fold are `<link rel="preload">`ed
- Every image has explicit dimensions and an `aspect-ratio` reservation
- `-webkit-text-size-adjust: 100%` stops iOS Safari inflating text on rotate
- `prefers-reduced-motion` disables the entrance and the marquee

## Accessibility

- Focus rings are per-surface: `--ink` on paper (13.8:1), `--accent` on the dark
  sections (6:1)
- Every section has an `aria-labelledby` pointing at a real heading
- Icons are inline SVG with `aria-hidden="true"`, not Unicode glyphs
- All body/functional text clears 4.5:1; mobile micro-labels have an 11px floor
- `forced-colors` needs no special handling: strokes are `currentColor` and the
  UA forces `outline-color`

## Deploy

Served by GitHub Pages from the repository root. Push to `main` and it goes
live at:

```
https://zeyadsleem.github.io/mohammed-sleem-portfolio/
```

## Credits

- Typefaces: Alexandria and Space Grotesk, SIL Open Font License 1.1
- Photography and project artwork: Mohammed Sleem, via
  [Behance](https://www.behance.net/mohammedsleem2)
- Icons: hand-authored inline SVG
