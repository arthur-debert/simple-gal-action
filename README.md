# simple-gal-action

GitHub Action to build photography galleries with [simple-gal](https://github.com/arthur-debert/simple-gal).

Downloads a pre-built binary from simple-gal releases and runs the build. No system dependencies needed — the binary is fully self-contained. Pair it with any deployment step (GitHub Pages, Netlify, S3, etc.).

## Usage

Create a content repo with your images under `content/` and add a workflow:

```yaml
name: Deploy Gallery

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - uses: actions/checkout@v4

      - name: Build gallery
        uses: arthur-debert/simple-gal-action@v1

      - uses: actions/upload-pages-artifact@v3
        with:
          path: dist

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

Push to `main` and your gallery is live.

## Inputs

| Input | Default | Description |
|-------|---------|-------------|
| `source` | `./content` | Path to content directory |
| `output` | `./dist` | Path to output directory |
| `version` | `latest` | simple-gal release version (e.g. `v0.2.0` or `latest`) |

## Custom domain

Add a `CNAME` file at the repo root with your domain, and copy it into the output before uploading:

```yaml
- name: Build gallery
  uses: arthur-debert/simple-gal-action@v1

- run: cp CNAME dist/

- uses: actions/upload-pages-artifact@v3
  with:
    path: dist
```

## How it works

1. Downloads the simple-gal binary from [GitHub Releases](https://github.com/arthur-debert/simple-gal/releases)
2. Runs `simple-gal build --source <input> --output <input>`

The action only builds. Deployment is handled by your workflow — use GitHub Pages, Netlify, Cloudflare Pages, S3, or any static host.

## Links

- [simple-gal](https://github.com/arthur-debert/simple-gal) — the gallery generator
- [Gallery documentation](https://github.com/arthur-debert/simple-gal#structuring-your-galleries) — how to structure content, configure themes, fonts, and colors
