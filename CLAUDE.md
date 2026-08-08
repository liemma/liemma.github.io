# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Emma Li's personal portfolio site, served by GitHub Pages from the `liemma/liemma.github.io` repo. It is public — anything committed here is published to the open web. It is a hand-written static site with no build step, no framework, no dependencies, and no tests — three tracked source files: `index.html`, `styles.css`, `images/profile.jpg`.

## Development

There is nothing to install or compile. Open `index.html` directly in a browser, or use the VS Code Live Server extension — `.vscode/settings.json` pins it to port 5502.

`main` is the deploy branch: pushing to `main` publishes the live site. Commit directly to `main` rather than opening a branch/PR, matching the existing history.

## Structure

`index.html` is the entire site — content and markup live inline, with no templating or data files. It is a single scrolling page with a sticky header whose nav links are in-page anchors to five sections: `#hero`, `#about`, `#experience`, `#projects`, `#contact`. Adding a section means adding both the `<section id="...">` and its nav `<li>` in the header.

Two repeated content patterns carry the bulk of the page:

- **Experience** — `.timeline` wraps a series of `.timeline-item` blocks, each with an `<h3>` title (linked to the employer when there is a URL), a `<p><strong>` date/location line in the form `Month YYYY – Month YYYY • City, ST`, and a `<ul>` of accomplishment bullets. Entries are ordered most recent first. `.timeline-item::before` draws the timeline dot, so the visual rail comes from CSS alone.
- **Projects** — `.projects-grid` wraps `.project` cards, each an `<h3>` (usually a link to a live demo or GitHub repo) plus one descriptive `<p>`.

Both patterns' items also carry the `fade-in-section` class. The inline `<script>` at the bottom of `index.html` is the only JavaScript: an `IntersectionObserver` that toggles `is-visible` on those elements as they scroll in and out of view. Any new item that should animate needs that class; nothing else registers it.

`styles.css` is plain CSS with no variables or preprocessor — colors are hardcoded per rule. The palette is white backgrounds with `#5c7db8` links (`#3e5ea4` hover) and `#1e3a8a` for the logo; the whole page uses the Google-hosted `Fira Code` font loaded via `<link>` in the head. Sections in the file are delimited by `/* Comment */` headers that roughly follow the page order.

## Content conventions

Prose in the About section uses typographic apostrophes and quotes (`’`, `“ ”`) rather than ASCII. Match that when editing existing copy.

Several `.timeline-item` divs have a stray double quote in the class attribute (`class="timeline-item fade-in-section""`). Browsers tolerate it and the styling works; leave it alone unless doing a deliberate cleanup pass.

External links use `target="_blank"`, most with `rel="noopener noreferrer"`.

Dated content goes stale — the About section, the footer year, and in-progress role descriptions ("currently", "incoming", "Present") need review whenever a date-bound fact changes.

## Git

Do not add `Co-Authored-By` trailers to commit messages in this repo.
