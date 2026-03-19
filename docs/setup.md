# Setup

Get publish-to-pages running on your machine and connected to your AI agent.

## Prerequisites

| Tool | Required for | Install |
|------|-------------|---------|
| `gh` CLI | Repo creation, Pages enablement | [cli.github.com](https://cli.github.com) |
| `python3` | PPTX and PDF conversion | Pre-installed on most systems |
| `python-pptx` | PPTX → HTML conversion | `pip install python-pptx` |
| `poppler-utils` | PDF → HTML conversion | `apt install poppler-utils` or `brew install poppler` |

Only `gh` is required for all workflows. The Python dependencies are only needed if you're converting PPTX or PDF files — HTML files publish directly.

## Installation

```bash
gh repo clone AndreaGriffiths11/publish-to-pages
```

Authenticate `gh` if you haven't already:

```bash
gh auth login
```

Install optional dependencies based on what you'll convert:

```bash
# PPTX support
pip install python-pptx

# PDF support (pick your OS)
apt install poppler-utils    # Debian/Ubuntu
brew install poppler         # macOS
```

## Configuring Your Agent

Point your agent at the `SKILL.md` file. The agent reads it, handles detection/conversion/publishing, and returns a live URL.

### Copilot CLI

```
Read the SKILL.md in ~/publish-to-pages and use it to publish my-slides.pptx to GitHub Pages
```

### Claude Code

```
Read ./publish-to-pages/SKILL.md and follow the instructions to publish my-talk.html to GitHub Pages
```

### Any other agent

```
Read [path-to]/publish-to-pages/SKILL.md and use it to publish [your-file] to GitHub Pages
```

The agent handles everything from there — input detection, conversion, repo creation, and Pages deployment.

## Verify It Works

Quick smoke test with a minimal HTML file:

```bash
echo '<h1>Hello Pages</h1>' > /tmp/test.html
bash publish-to-pages/scripts/publish.sh /tmp/test.html test-pages-smoke public "Smoke test"
```

If that succeeds, you'll see:

```
REPO_URL=https://github.com/YOUR_USERNAME/test-pages-smoke
PAGES_URL=https://YOUR_USERNAME.github.io/test-pages-smoke/
```

Clean up after:

```bash
gh repo delete test-pages-smoke --yes
```
