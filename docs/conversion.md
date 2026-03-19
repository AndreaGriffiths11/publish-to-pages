# Format Conversion Guide

## Supported Formats

| Format | Extension / Input | Conversion | Dependencies |
|--------|------------------|------------|-------------|
| HTML | `.html`, `.htm` | None — published as-is | — |
| PPTX | `.pptx` | `convert-pptx.py` → HTML | `python-pptx` |
| PDF | `.pdf` | `convert-pdf.py` → HTML | `poppler-utils` (`pdftoppm`) |
| Google Slides | URL | Downloaded as PPTX, then converted | `python-pptx` |

## PPTX Conversion

The `convert-pptx.py` script parses the PPTX file and generates an HTML presentation that preserves slide layout and positioning.

### What's preserved

- Text formatting: bold, italic, underline, font size, font family, color
- Text alignment (left, center, right, justify)
- Images (PNG, JPEG, GIF, BMP, WebP, SVG)
- Slide backgrounds (solid colors from slide and layout)
- Tables with cell borders
- Shape fills (solid colors)
- Shape positioning (absolute, percentage-based)

### What's not preserved

- Animations and transitions
- Embedded videos and audio
- SmartArt diagrams
- Charts (they render as images only if embedded as pictures)
- Gradient fills (only solid fills are extracted)
- Grouped shape hierarchies
- Speaker notes

```bash
python3 scripts/convert-pptx.py presentation.pptx /tmp/output.html
```

## PDF Conversion

`convert-pdf.py` uses `pdftoppm` from poppler-utils to render each page as a PNG image, then wraps them in a navigable HTML presentation.

The default DPI is 150. Higher DPI means sharper images but larger output:

```bash
# Default (150 DPI)
python3 scripts/convert-pdf.py document.pdf /tmp/output.html

# Higher quality (200 DPI)
python3 scripts/convert-pdf.py document.pdf /tmp/output.html --dpi 200

# Lower quality, smaller file (100 DPI)
python3 scripts/convert-pdf.py document.pdf /tmp/output.html --dpi 100
```

## Google Slides

Google Slides are downloaded as PPTX, then converted with the PPTX script.

The presentation must be publicly accessible (or at least viewable by anyone with the link). The export URL is constructed from the presentation ID:

```bash
# Extract the ID from the URL (the long string between /d/ and /)
curl -L "https://docs.google.com/presentation/d/PRESENTATION_ID/export/pptx" -o /tmp/slides.pptx

# Then convert
python3 scripts/convert-pptx.py /tmp/slides.pptx /tmp/output.html
```

Your agent handles this automatically — just give it the Google Slides URL.

## External Assets Mode

By default, images are embedded as base64 inside the HTML file. This works fine for small presentations but creates problems at scale:

- GitHub rejects files over 100MB
- Base64 encoding inflates file size by ~33%
- Browsers slow down loading megabytes of inline data

External assets mode saves images as separate files in an `assets/` directory alongside the HTML.

### Auto-detection

The conversion scripts automatically enable external assets mode when:

- The input file is larger than **20MB**, or
- The presentation has more than **50 images/pages**

Files over **150MB** trigger a warning — for PPTX, the script suggests using PDF conversion instead.

### Manual override

Force external assets on or off:

```bash
# Force external assets (useful for medium files you know will be large)
python3 scripts/convert-pptx.py big-deck.pptx /tmp/output.html --external-assets

# Force inline (if you want a single self-contained file)
python3 scripts/convert-pptx.py small-deck.pptx /tmp/output.html --no-external-assets
```

### How the assets/ directory works

When external assets mode is active:

1. Images are saved as `assets/img-001.png`, `assets/img-002.jpg`, etc.
2. The HTML references them with relative paths (`src="assets/img-001.png"`)
3. `publish.sh` automatically detects and pushes the `assets/` directory alongside `index.html`

The HTML and `assets/` directory must stay in the same parent directory for paths to resolve.

## Output HTML Features

Both conversion scripts produce HTML with built-in navigation:

| Feature | How it works |
|---------|-------------|
| Keyboard navigation | Arrow keys and spacebar to advance/go back |
| Touch/swipe | Swipe left/right on mobile devices |
| Click navigation | Click right half to advance, left half to go back |
| Progress bar | Blue bar at the bottom showing position |
| Slide counter | "3 / 12" indicator in the bottom-right corner |
| Responsive scaling | PPTX slides scale to fit any screen size |
