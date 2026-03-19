# Troubleshooting

## python-pptx not installed

```
ERROR: python-pptx not installed. Install with: pip install python-pptx
```

Fix:

```bash
pip install python-pptx
```

If you're using a virtual environment, make sure it's activated first.

## poppler-utils / pdftoppm missing

```
Error: pdftoppm not found. Install poppler-utils
```

Fix:

```bash
apt install poppler-utils    # Debian/Ubuntu
brew install poppler         # macOS
```

## gh not authenticated

```
MISSING: gh not authenticated — run 'gh auth login'
```

Fix:

```bash
gh auth login
```

Follow the prompts to authenticate via browser or token.

## Repo already exists

```
ERROR: Repository username/repo-name already exists
```

The publish script won't overwrite an existing repo. Options:

- Pick a different name (`my-slides-2`, `my-slides-2026`)
- Delete the old repo first: `gh repo delete username/repo-name --yes`

## GitHub Pages not deploying

Pages typically takes 1-2 minutes to go live after pushing. If it's been longer:

1. Check the Actions tab: `https://github.com/USERNAME/REPO/actions`
2. Look for a "pages build and deployment" workflow
3. If it failed, check the error logs in the workflow run

If Pages wasn't enabled at all, enable it manually:
- Go to repo **Settings → Pages → Source**
- Set branch to `main`, folder to `/ (root)`

## Output file too large (>100MB)

GitHub rejects individual files over 100MB. This usually happens with large PPTX files that have many high-resolution images.

Fix — use external assets mode:

```bash
python3 scripts/convert-pptx.py large-deck.pptx /tmp/output.html --external-assets
```

This saves images as separate files in `assets/` instead of embedding them as base64. The publish script handles pushing the `assets/` directory automatically.

## Google Slides download fails

```
curl: (22) The requested URL returned error: 404
```

The presentation isn't publicly accessible. Either:

- Make it viewable: **Share → Anyone with the link → Viewer**
- Download the PPTX manually from Google Slides (**File → Download → .pptx**) and convert locally

## Private repo Pages requires paid plan

GitHub Pages works on private repos only with Pro, Team, or Enterprise plans. On a free plan, the repo must be public for Pages to work.

If you need the repo private, either:

- Upgrade your GitHub plan
- Use `public` visibility (the default) — the repo contents are public but that's the tradeoff

## Images not showing in published presentation

If the presentation loads but images are broken:

1. Check that `assets/` was pushed to the repo (look at the repo on GitHub)
2. Verify the HTML file references `assets/img-001.png` (relative paths, no absolute paths)
3. Make sure the HTML file and `assets/` directory were in the same parent directory when `publish.sh` ran

## PPTX elements not rendering

Some PPTX features don't convert to HTML. See the [conversion guide](conversion.md) for the full list of what's preserved and what isn't.

Common gaps:

- Charts render only if they were saved as embedded images in the PPTX
- SmartArt diagrams won't appear
- Gradient backgrounds fall back to white
- Animations are ignored (slides show final state)
