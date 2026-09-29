# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Emma Li's personal portfolio site, served by GitHub Pages from the `liemma/liemma.github.io` repo. It is public — anything committed here is published to the open web. It is a hand-written static site with no build step, no framework, no dependencies, and no tests: `index.html`, `styles.css`, and `images/`.

## Development

There is nothing to install or compile. Open `index.html` directly in a browser, or serve the directory (`python3 -m http.server 5502`) — `.vscode/settings.json` pins Live Server to the same port.

`main` is the deploy branch: pushing to `main` publishes the live site. Commit directly to `main` rather than opening a branch/PR, matching the existing history.

## Structure

The site is a terminal-styled single page. Everything lives inside one `.terminal` window: a title bar, a `.tabbar`, and a `.terminal-body` holding six `.pane` sections — `whoami`, `about`, `experience`, `teaching`, `projects`, `contact`.

**Panes are tabs, not scroll targets.** Exactly one pane is visible; the rest carry the `hidden` attribute. The inline `<script>` at the bottom of `index.html` owns all of it. Adding a section means adding both a `.pane` section and a `.tab` button whose `data-pane` matches the pane's `id` — the script derives everything else from those two attributes.

The active pane is mirrored into the URL hash via `history.replaceState`, so `#projects` deep-links and the back button works. Anything else that should jump to a pane just needs `data-goto="<pane-id>"`; the script wires the click handler.

Repeated content patterns:

- **Experience** — styled as `git log` output. `.commits` wraps `.commit` articles, each with a `.commit-line` (fake short hash, optional `.refs`), an `.org-mark` logo, `<h3>` role, `.meta` key/value lines, a `.diff` list whose items render as green `+` diff additions, and a `.stack` line. Most recent first; `.commit::before` draws the graph node.
- **Teaching** — `.roster` is a `<ul>` whose items are CSS-grid rows: `.r-term`, `.r-role`, `.r-course`, `.r-prof`, with a `.roster-head` label row on top. Reverse-chronological; the in-progress appointment carries `.current`, which tints the role and appends a dot to the term. Under 780px the grid collapses to stacked blocks — note that rule hides the header as `.roster li.roster-head`, because the bare class loses to `.roster li` on specificity.
- **Projects** — `.projects-grid` wraps `.project` articles, each a miniature terminal pane: a `.file-line` header reading `$ cat <dir>/README.md`, then a `.project-body` with `<h3>` link, `<p>`, and a `.project-tags` row rendered as `#tag` chips. Each card needs `data-tags="A,B"`. The filter chips above the grid are **generated from those attributes at runtime**, so a new tag needs no JS change; filtering toggles `.filtered-out`.

`.org-mark` is the employer logo slot. It holds either a real SVG (`images/doordash.svg`, `images/columbia.svg`) sized by a `height` attribute, or a `.org-text` span — a small-caps text wordmark — for orgs with no logo file. Monochrome marks carry `.mono`, which flips them to white in dark mode via a CSS filter; colored marks like DoorDash's are left alone because they read on both palettes.

## styles.css

Themed through CSS custom properties on `:root`. Light is the base palette; dark is redefined twice — under `@media (prefers-color-scheme: dark)` guarded by `:root:not([data-theme="light"])`, and again under `:root[data-theme="dark"]` so the toggle wins either way. **Never give a color its only definition inside one of those blocks.** A blocking script in `<head>` stamps `data-theme` before first paint (defaulting to dark) to avoid a flash.

Two type families: `--mono` (Fira Code) for headings, UI chrome, the `whoami`/`about` panes, and meta lines; `--sans` for longer prose in the experience and project bodies, where monospace at paragraph length gets tiring.

## Favicon

`favicon.svg` at the repo root, linked as `/favicon.svg` — root-absolute, which resolves both on `liemma.github.io` (a user page served from `/`) and on an apex custom domain. It is an "EL" monogram drawn as plain `<rect>`s on a 32-unit grid rather than `<text>`, so it needs no font and stays crisp at 16px. Its embedded `<style>` swaps the palette under `prefers-color-scheme`; browsers that ignore media queries in SVG favicons get the light pair, which reads on either tab strip.

## Gotchas

The inline script is one IIFE with `var` declarations. `show()` runs during initialisation and calls `updateProgress()`, so **anything `show()` touches must be declared above the tab block** — a `var` used before its assignment is `undefined`, throws, and silently kills the rest of the script (including the filter-chip builder). This has already caused one bug.

Prose uses typographic apostrophes and quotes (`’`, `“ ”`) rather than ASCII. Match that when editing existing copy.

Dated content goes stale — the About section, the footer year, and in-progress roles ("currently", "Present") need review whenever a date-bound fact changes.

## Publishing judgment

This repo publishes to the open web, and the work described on it touches an employer's internal systems. Keep unannounced partner names, internal product details, and screen recordings of internal UI off the site unless the user confirms they are cleared for public sharing. When something looks like it might not be public yet, ask rather than assume.

## Git

Do not add `Co-Authored-By` trailers to commit messages in this repo.
