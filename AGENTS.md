# AGENTS.md

This file provides guidance to Codex (Codex.ai/code) when working with code in this repository.

## Project overview

A single-page personal portfolio site built with vanilla HTML, CSS, and JavaScript. No frameworks, no build step.

## Development

Serve with any static file server:

```bash
npx serve .
```

Then open the displayed URL (typically http://localhost:3000).

There are no build, lint, or test commands.

## Architecture

Three flat files in the root directory:

- `index.html` — All markup: sticky nav, hero, About/Projects/Skills/Contact sections, footer. Uses Google Fonts (Inter).
- `style.css` — All styling. CSS custom properties on `:root` for the color scheme/spacing. Responsive via a single `@media (max-width: 600px)` breakpoint at the bottom of the file.
- `script.js` — Two small vanilla JS behaviors: smooth scroll for in-page anchor links, and a contact form submit handler that shows a "Sent!" confirmation for 2 seconds before resetting (no actual backend — purely cosmetic).

No external JS dependencies. No CSS framework. All CSS is hand-written. The contact form does not post anywhere.
