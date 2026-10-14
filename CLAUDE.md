# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

A single-page personal portfolio site for Pragendra Nakarmi. The entire site is one self-contained file, `index.html`: markup, an inline `<style>` block, and inline scripts. There is no build step, package manager, test suite, or linter. It is a git repository pushed to GitHub, and Netlify auto-deploys every push. `netlify.toml` publishes the repo root with no build command.

## Animations (Motion)

Animations use the vanilla JS build of [Motion](https://motion.dev) (`animate`, `scroll`, `inView`, `hover`, `stagger`), imported from jsDelivr in the module script at the end of `<body>`, pinned to `motion@12.43.0`. Transition options follow https://motion.dev/docs/react-transitions; `animate(target, values, transition)` takes the same transition object as React's `transition` prop.

The "galaxy" look comes from fixed, `pointer-events: none` layers placed before the header: a `.galaxy` canvas, `.nebula` blobs, a `.cursor-glow`, and a `.scroll-progress` bar. The galaxy canvas is a plain inline script, not Motion, so it renders even without the CDN. It draws a 3-arm spiral of particles in 3D and reads scroll progress through the pinned `.intro` section (`height: 260vh`, with a sticky inner frame). As that progress goes from 0 to 1, it rotates the galaxy, flies the camera into the core, then dims so the rest of the page reads over it. The camera tuning lives in the `draw()` function: `dist`, `tilt`, `spin` and `brightness`. Motion separately scroll-links the intro text fade and blur to the same section. `main` and the footer sit above them at `z-index: 1`. Hover effects (card tilt and spotlight via `--mx`/`--my`/`--glow`, magnetic buttons, pills) set inline transforms with Motion, so don't add CSS `transition: transform` to those elements. Reveal tweens animate opacity and blur instead of springs, because spring overshoot gives invalid blur values.

- A small script in `<head>` adds `motion-ready` to `<html>`, unless the user prefers reduced motion. CSS hides the elements that will animate only while that class is present.
- If Motion doesn't load (import error, or `window.motionStarted` not set within 3s), the class is removed so content stays visible. When adding new animated elements, update both the `.motion-ready :is(...)` CSS selector and the module script.

## Running

Open `index.html` directly in a browser. To serve it locally instead, use any static server, e.g. `python -m http.server` from the repo root.

## Structure and conventions

- **Styling is token-driven.** Colors and shadows are CSS custom properties on `:root` (`--bg`, `--panel`, `--white`, `--muted`, `--primary`, `--primary-2`, `--accent`, `--border`, `--shadow`). Reuse these rather than adding hard-coded colors. The site is dark-theme only, with an Astra-style palette: near-black `--bg`, white/grey text, white pill buttons (`--primary`), and color limited to lavender (`--primary-2`) and warm starlight (`--accent`), matching the galaxy's particle colors (`stops` in the galaxy script).
- **Layout primitives:** `.container` (max width 1120px with a 16px gutter), `.grid-3` / `.about-grid` (3-column grid), `.card` (the panel used by every section), `.pill` (skill/tag chips), and `.button.primary` / `.button.secondary`.
- **Sections** live inside `<main id="top">` in this order: hero, `#about`, `#skills`, `#projects`, `#experience`, `#contact`. Each content section uses a `.container > .section-header (h2 + p)` followed by its content. The header nav links to these section ids, so update the nav if you add, rename, or remove a section.
- **Responsive breakpoints** sit at the end of the `<style>` block: at ≤900px the grids collapse to one column and the nav is hidden; at ≤560px the hero buttons stack full-width and the stats become a 2-column grid. New multi-column layouts should be added to the 900px rule.
- Project visuals (`.project-visual`) are CSS-only placeholders drawn with `::before`/`::after`, and the hero `PN` badge is text, so the site has no image assets yet.
- Content is placeholder-like copy (stats, projects, timeline dates, and the `mailto:` address). Treat it as the owner's content and don't change it unless asked.
