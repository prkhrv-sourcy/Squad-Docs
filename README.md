# Squad documentation

Public documentation and release notes for Squad.

**Website:** https://prkhrv-sourcy.github.io/Squad-Docs/

## Publish an update

1. Add an HTML document with a descriptive filename. For release notes, use
   `release-YYYY-MM-DD-short-title.html`; copy an existing release as a starting point.
2. Link it from `index.html`, with the newest release first. Use relative links
   so they work under the site's `/Squad-Docs/` path.
3. Commit and push to `main`. GitHub Pages publishes the update automatically.

Keep existing filenames stable so shared links continue to work. This site is
public; only commit documentation intended for public sharing.

## Preview locally

Run `python3 -m http.server 8000` from this directory, then open
http://localhost:8000.

## Hosting

GitHub Pages serves the root of `main` using **Deploy from a branch** in
**Settings → Pages**. `.nojekyll` serves the HTML directly. There are no build
dependencies or custom workflows.
