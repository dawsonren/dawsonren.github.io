# dawsonren.github.io

Source for [dawsonren.github.io](https://dawsonren.github.io), Dawson Ren's portfolio site.
Built with [Astro](https://astro.build) as a fully static site with no client-side JavaScript,
and deployed to GitHub Pages by GitHub Actions on every push to `main`.

## Run locally

Requires Node 22.12 or newer.

```sh
npm install
npm run dev       # dev server at http://localhost:4321
npm run build     # static build into dist/
npm run preview   # serve the built dist/ locally
```

## Where things live

| Path | What it is |
|---|---|
| `src/pages/index.astro` | Homepage: hero, selected work, also, writing, about |
| `src/pages/resume.astro` | Resume as HTML, transcribed from the PDF. Update both together |
| `src/pages/projects/*.md` | Case studies |
| `src/pages/writing/`, `src/pages/archive/` | Current and archived posts |
| `src/layouts/Article.astro` | Layout for case studies and posts (set via `layout:` front matter) |
| `src/styles/global.css` | Design tokens and base styles |
| `src/site.ts` | Name, email, and profile links used across the site |
| `public/resume/Dawson-Ren-Resume.pdf` | The linked resume PDF. **Keep this filename**, since it's linked from elsewhere |
| `public/img/headshot.jpg` | Homepage photo (square, 800×800). If it's missing, a placeholder box is shown |
| `public/og.png` | 1200×630 social preview card |
| `astro.config.mjs` | Site URL, sitemap, and redirects from the old Jekyll post URLs |

## Deploying

Pushing to `main` runs `.github/workflows/deploy.yml`. In the repo's Settings → Pages,
**Source** must be set to **GitHub Actions**.
