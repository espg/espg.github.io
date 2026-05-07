# Shane Grigsby's blog

mystmd port of a formerly Sphinx + ablog site.
Deployed to GitHub Pages via the workflow in `.github/workflows/deploy.yml`.

## Local development

The build pipeline depends on three vendored repos pinned as submodules:

| Submodule | Pinned to | Why |
| --- | --- | --- |
| `vendor/blog-plugin` | `espg/blog-plugin @ feature/notebook-frontmatter` | Stack of three additive PRs we'll propose to upstream `jupyter-book/blog-plugin` (filter options, `{update}` directive, notebook frontmatter parsing). |
| `vendor/myst-theme` | `espg/myst-theme @ feature/blog-theme-features` | Local fork that adds `themes/blog-theme/` — sibling to upstream `book-theme` with a non-dismissible site `header` part, `hide_navbar` option, `pin_theme` option, and a JS scroll listener that offsets sidebars by the banner height. |
| `vendor/mystmd` | `jupyter-book/mystmd PR #2770` (`site-static-files` branch) | Adds the `site.static_files` option needed to serve `Banner.png` at a stable URL. Drop this submodule when the PR ships in a release. |

### One-time setup after clone

```bash
git submodule update --init --recursive
cd vendor/mystmd && bun install && bunx turbo run build && cd ../..
cd vendor/blog-plugin && npm install && npm run build && cd ../..
```

(myst-theme self-installs and self-builds on first `myst build` via its
`template.yml` `build.install` hook, so you don't need to manually
install it.)

### Building the site

```bash
node vendor/mystmd/packages/mystmd/dist/myst.cjs build --html
```

Output: `_build/html/`. Serve locally with `python3 -m http.server 8765` from there.

### Editing content

- Add posts as `.md` or `.ipynb` with the frontmatter described in
  [`vendor/myst-theme/themes/blog-theme/template.yml`](vendor/myst-theme/themes/blog-theme/template.yml)
  and [the project's DESIGN.md](https://github.com/espg/myst-blog-theme/blob/main/DESIGN.md).
- Update `myst.yml` `toc` for navigation order.
- Banner.png lives at the project root and is shipped via `site.static_files`.

## Deployment

Pushing to `main` triggers `.github/workflows/deploy.yml`:

1. Checks out blog repo with all three submodules.
2. Installs bun, builds the patched mystmd CLI.
3. Builds `blog-plugin/dist/plugin.mjs`.
4. Runs `myst build --html` (myst-theme self-builds via the install hook).
5. Generates redirect HTML files for old Sphinx-era URLs.
6. Uploads the static output and deploys to GitHub Pages.

### Adding redirects from old URLs

Edit the `REDIRECTS` array in `.github/workflows/deploy.yml`:

```yaml
declare -a REDIRECTS=(
  "old/path.html|/new-slug"
)
```

Each entry becomes an HTML file at the old path with a meta-refresh to the new path.

## Open issues

- Three blog-plugin PRs (filters, `{update}`, notebook frontmatter) need to be opened against `jupyter-book/blog-plugin` upstream.
- mystmd PR #2770 (`site.static_files`) needs to land + ship in a release before we can drop the `vendor/mystmd` submodule.
- `myst-blog-archives` plugin is the major remaining ablog-parity feature (auto tag/year archive pages); not yet built.
