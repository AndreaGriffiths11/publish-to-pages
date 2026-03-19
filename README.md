# publish-to-pages

Give it a file. Get a live GitHub Pages URL.

An agent skill that publishes presentations and web content to GitHub Pages. Works with any AI coding agent that reads skill files.

![Architecture](architecture.jpg)

## Quick Start

**1. Clone**

```bash
gh repo clone AndreaGriffiths11/publish-to-pages
```

**2. Authenticate**

```bash
gh auth status
```

**3. Tell your agent**

```
Read the SKILL.md in ~/publish-to-pages and use it to publish my-slides.pptx to GitHub Pages
```

The agent handles detection, conversion, repo creation, and deployment.

## Supported Inputs

| Input | What happens |
|-------|-------------|
| HTML (.html, .htm) | Published directly |
| PPTX (.pptx) | Converted to HTML with formatting preserved |
| PDF (.pdf) | Pages rendered as images in navigable HTML |
| Google Slides (URL) | Exported as PPTX, then converted |

## Documentation

| Doc | Description |
|-----|-------------|
| [Setup](docs/setup.md) | Installation & agent configuration |
| [Conversion](docs/conversion.md) | Format support, external assets, output features |
| [Architecture](docs/architecture.md) | How the pieces fit together |
| [Troubleshooting](docs/troubleshooting.md) | Common issues & fixes |
| [SKILL.md](SKILL.md) | Agent skill instructions |

## Links

- [Demo](https://andreagriffiths11.github.io/publish-to-pages-site/)
- [awesome-copilot](https://github.com/github/awesome-copilot/tree/main/skills/publish-to-pages)
