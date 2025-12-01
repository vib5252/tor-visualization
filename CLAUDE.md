# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Hugo-based static site that visualizes Tor relay analysis using unsupervised learning and trust scoring. The project applies data science techniques (PCA, t-SNE, UMAP, HDBSCAN, RBM) to Tor network data from the public Onionoo API to identify behavioral patterns, anomalies, and temporal drift in relay clusters.

The site is deployed to GitHub Pages at: https://vib5252.github.io/tor-visualization/

## Build and Development Commands

### Local Development
```bash
# Start Hugo development server with drafts
hugo server -D

# Build the site (output goes to docs/)
hugo --minify

# Build without minification
hugo
```

### Configuration
- `config.toml` contains all site configuration
- `publishDir = "docs"` means builds output to the `docs/` directory (for GitHub Pages)
- Theme: `mostafa-hugo-theme` (included as submodule)

### Deployment
The site auto-deploys via GitHub Actions on push to main:
- Workflow: `.github/workflows/gh-pages.yml`
- Builds with `hugo --minify`
- Deploys from `docs/` directory

## Architecture

### Content Structure
- **Posts**: `content/posts/*.md` - Main content pages explaining the analysis pipeline
  - `intro.md` - Overview of the pipeline and techniques
  - `goal.md` - System architecture and evolution diagram
  - `pca.md` - Principal Component Analysis explanations
  - `geometry-of-change.md` - Temporal dynamics and stability concepts
  - `drift.md`, `which_cluster.md` - Analysis methodology

- **Plots**: `static/plots/*.html` - Interactive Plotly visualizations
  - HDBSCAN cluster visualizations on t-SNE/UMAP embeddings
  - RBM energy overlay plots showing anomaly patterns
  - PCA feature analysis (HSDir, Exit, uptime patterns)
  - Geographic relay distribution map

### Layout Customizations

The site uses the `mostafa-hugo-theme` with custom overrides:

- `layouts/index.html` - Custom homepage with pinned posts logic
- `layouts/_default/baseof.html` - Base template with MathJax integration for LaTeX rendering
- `layouts/partials/head.html` - Additional MathJax configuration
- `static/css/custom.css` - Custom styling (hides copy buttons, adjusts ASCII diagrams)

### Theme Configuration

Key theme params in `config.toml`:
- Dark theme by default with custom color palette (yellow-green accents)
- MathJax enabled for inline math: `$...$` or `\\(...\\)`
- Menu and search disabled
- Custom text/header/background colors for data visualization aesthetic

### Front Matter Conventions

Posts use these front matter fields:
- `excludefromindex: true` - Hide from homepage listing
- `weight: N` - Control ordering (lower = higher priority)
- `math: true` - Enable MathJax on specific pages
- `tags: ["tag"]` - Categorization

### Data Science Pipeline

The content describes a multi-stage analysis pipeline:
1. Raw Tor relay signals (flags, bandwidth, uptime, geo, policies)
2. Preprocessing (clean, align, normalize)
3. Representation learning (dimensionality reduction)
4. Topology mapping (t-SNE/UMAP for neighborhood structure)
5. Behavioral clustering (HDBSCAN for density-based groups)
6. Temporal dynamics (tracking cluster evolution)
7. Stability & drift detection
8. Visualization layer

Key algorithms referenced:
- **PCA**: Linear dimensionality reduction, noise reduction for t-SNE/UMAP input
- **t-SNE**: Local structure preservation (non-linear embedding)
- **UMAP**: Local + global structure preservation
- **HDBSCAN**: Hierarchical density clustering (works on embedded spaces)
- **RBM**: Restricted Boltzmann Machine for latent pattern extraction and anomaly scoring

### Linking to Plots

Use absolute paths from site root:
```markdown
[Plot Title](/plots/filename.html)
```

Use relref for internal content:
```markdown
[PCA]({{< relref "posts/pca.md" >}})
```

### HTML in Markdown

The theme allows `unsafe = true` in goldmark renderer, so you can embed raw HTML for:
- Custom div containers with overflow-x for ASCII diagrams
- Inline styles for layout control

## Important Notes

- All visualization HTML files are large (5-27 MB) and should remain in `static/plots/`
- MathJax is globally loaded in `baseof.html` for LaTeX equation rendering
- The site focuses on visual storytelling with minimal navigation
- Content emphasizes "why" and conceptual understanding over implementation details
