# Claire Hesten — Photography Portfolio

A quiet, editorial photography portfolio site. Static HTML/CSS, no build step, no framework — implemented from a Figma design.

## Structure

```
.
├── index.html            Homepage — hero video, "My Recents", Dalmatian Coast, etc.
├── mediterranean.html     Album page
├── alpine.html            Album page
├── tokens.css             Design tokens (colour, spacing, chip styles)
├── styles.css             Layout and components
├── icons/                 Exported SVG icons (view-album, back arrow)
├── images/                Photography, resized/compressed for web;
│                          images/originals/ holds the full-resolution source
└── media/                 Video (web-optimised); media/originals/ holds the
                           untouched source files these were compressed from
```

## Running locally

No build step — just serve the folder so relative paths resolve correctly (opening `index.html` directly via `file://` will break the video/image paths on some browsers).

```bash
python3 -m http.server 4173
```

Then open `http://localhost:4173`.

## Design system

- **Type**: Cormorant Garamond (serif, headings/logotype) + Inter (body/labels) + Roboto (chip labels), loaded via Google Fonts.
- **Tokens** (`tokens.css`): primitives (raw colour/spacing values) → semantic tokens (`--surface-page`, `--content-gutter`, `--chip-*`, etc.). Always prefer a semantic token over a primitive when styling.
- **Grid images**: sized via `aspect-ratio` on `.figure__media`. Most rows share one uniform box (`.figure--grid`, currently `2201 / 2731`) so images line up; a row can opt out by setting its own `aspect-ratio` inline on the `<img>` to preserve that photo's native crop instead.
- **Videos**: `.figure--banner` (full-bleed hero) and `.figure--wide` (in-page) are both locked to `16 / 9`, matching the native resolution of the source clips — don't change these without re-checking the actual video dimensions.

Source Figma file: *Photography Portfolio* (nodes referenced during implementation: `1:69`, `20:14`, `17:882`, `17:1013`).

## Media

Videos in `media/` are re-encoded (H.264, CRF 23, `+faststart`) from the originals in `media/originals/` to keep page weight reasonable. If you replace a video, consider compressing it the same way:

```bash
ffmpeg -i input.mp4 -c:v libx264 -preset slow -crf 23 -pix_fmt yuv420p -movflags +faststart output.mp4
```

Photos in `images/` are similarly resized/recompressed from the full-resolution originals in `images/originals/` — 1600px on the long edge, quality 82, which comfortably covers the largest size any photo displays at (the grid columns) even at 2x/3x pixel density. If you add a new photo, process it the same way before committing:

```bash
sips -Z 1600 -s formatOptions 82 input.jpg --out output.jpg
```

Every `<img>` on the homepage also uses `loading="lazy" decoding="async"`, so only images near the viewport are fetched as the visitor scrolls, rather than all of them at once.

## Known gaps

- The **View Album** chips on the homepage currently link to `#` (no target page wired up yet).
- `mediterranean.html` and `alpine.html` exist and render, but nothing on the homepage links to them at the moment.
- The full-resolution originals in `images/originals/` and `media/originals/` are still very large (tens of MB, one over 100MB) — fine for local dev, but will need Git LFS or exclusion before pushing to GitHub, since GitHub hard-blocks files over 100MB.
