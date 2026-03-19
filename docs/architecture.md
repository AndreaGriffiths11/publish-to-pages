# Architecture

How publish-to-pages turns a file into a live URL.

## Flow

```
Input File → Detection → Conversion → Publishing → Live URL
(.html/.pptx/.pdf/URL)   (by extension)  (to HTML)    (gh CLI)   (GitHub Pages)
```

![Architecture](../architecture.jpg)

## File Structure

```
publish-to-pages/
├── SKILL.md              # Agent instructions (the skill definition)
├── README.md             # Project overview
├── architecture.jpg      # Flow diagram
├── docs/                 # Documentation (you are here)
│   ├── setup.md
│   ├── conversion.md
│   ├── architecture.md
│   └── troubleshooting.md
└── scripts/
    ├── publish.sh        # Creates repo, pushes content, enables Pages
    ├── convert-pptx.py   # PPTX → HTML with formatting preserved
    └── convert-pdf.py    # PDF → HTML via page-to-PNG rendering
```

## Input Detection

The agent determines the input type by file extension or URL pattern:

| Input | Detection rule |
|-------|---------------|
| HTML | `.html` or `.htm` extension |
| PPTX | `.pptx` extension |
| PDF | `.pdf` extension |
| Google Slides | URL contains `docs.google.com/presentation` |

HTML files skip conversion entirely and go straight to publishing.

## How publish.sh Works

The publish script (`scripts/publish.sh`) takes four arguments:

```bash
bash scripts/publish.sh <html-file> <repo-name> <visibility> <description>
```

What it does, step by step:

1. Gets your GitHub username via `gh api user`
2. Checks if the repo already exists (fails if it does)
3. Creates the repo with `gh repo create`
4. Clones the new repo to a temp directory
5. Copies `index.html` into it
6. If an `assets/` directory exists alongside the HTML file, copies that too
7. Commits and pushes to `main`
8. Enables GitHub Pages on the `main` branch root via the GitHub API
9. Prints the repo URL and Pages URL
10. Cleans up the temp directory

The Pages API call (`POST /repos/{owner}/{repo}/pages`) is wrapped in `|| true` — if it fails (permissions, plan limitations), the script still succeeds and returns the repo URL. The user can enable Pages manually from repo settings.

## How the HTML Presentations Work

Both conversion scripts generate self-contained HTML files with the same navigation system.

### Slide rendering

- PPTX: Each slide becomes a `<section>` with absolutely-positioned `<div>` elements for text, images, and tables. A `.slide-inner` container maintains the original aspect ratio and scales to fit the viewport.
- PDF: Each page is rendered as a PNG image centered in a `<section>`.

### Navigation

A small JavaScript block at the end of the file handles all navigation:

- Maintains a `current` slide index
- `show(n)` hides all slides, shows the target, updates the progress bar and counter
- Keyboard listener: `ArrowRight`/`Space` → next, `ArrowLeft` → previous
- Touch listener: tracks `touchstart`/`touchend` X positions, swipe threshold of 50px
- Click listener: right half of screen → next, left half → previous

### Scaling (PPTX only)

PPTX presentations use a fixed-size `.slide-inner` (1280px wide, height calculated from the PPTX aspect ratio). A `scaleSlides()` function runs on load and resize, calculating `Math.min(scaleX, scaleY)` and applying a CSS `transform: scale()` to fit the viewport without distortion.

PDF presentations use `max-width: 100%; max-height: 100%; object-fit: contain` on the page images instead — simpler since each page is just an image.
