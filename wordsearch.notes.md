# Markov Wordsearch — Notes

## Overview

A wordsearch generator/player driven by a Markov-chain letter model trained
on a reference text, with target words embedded into the grid. The UI is
split into four modes: **Design**, **Watch**, **Play**, and **Collapse**.

## Page structure (`index.html`)

- `<aside>` — sidebar containing mode tabs and all configuration panels.
  - `.mode-tabs` — buttons (`data-mode="design|watch|play|collapse"`) that
    toggle the corresponding `#panel-*` section.
  - `#panel-design` — generation controls (Generate, Export TXT) and
    `#status` output.
  - `#panel-watch` — step-through visualization of the grid buildout
    (Step / Play / Pause / Finish / Reset) with adjustable speed
    (`#watch-speed`) and `#watch-status`.
  - `#panel-play` — timed word-finding game: timer, target word list
    (`#play-words`), optional bonus word list (`#play-bonus-wrap`),
    pause/new game actions, and `#play-status`.
  - `#panel-collapse` — "Collapse" mode: find any valid word or
    run of 3+ identical/consecutive letters; matches are cleared, tiles
    drop, and new letters fill in. Includes timer, score, found list,
    pause/new game controls.
  - `#config-controls` — shared configuration, always visible:
    - **Grid**: width/height (`cfg-width`, `cfg-height`).
    - **Model**: Markov order (`cfg-order`), lattice type (square/hex),
      backwards-direction toggle, combiner (vote/product/sum/max),
      sampling strategy (weighted/argmax).
    - **Reference text**: preset picker, URL loader, file upload, or
      pasted text (`cfg-reftext`).
    - **Target words**: URL loader, textarea, word count limit, debug
      highlight toggle, max word adjacency.
    - **Global word list**: URL for real-word avoidance list, toggle to
      disable avoidance (recommended when using Collapse mode).
    - **Randomness**: optional seed for repeatable/shareable puzzles.
    - **Text**: font scale slider and font-family selector.
- `<main>` — `#grid` container where the generated table is rendered.
- Footer/utility: a fixed `.home-link` anchor (top-right) linking back to
  the site root (`/`).
- Entry point script: `./src/index.js` (ES module).

## Styling (`style.css`)

- Dark, glassmorphic "vibrant" theme using CSS custom properties for
  palette, radius, shadow, and easing (`:root` variables).
- Animated gradient background (`bg-drift`) plus a subtle starfield
  overlay (`body::before`).
- Sidebar (`aside`) is a frosted-glass panel with custom scrollbar
  styling and a resize handle (`.sidebar-resize-handle`) allowing the
  user to drag-resize the sidebar width (adds `body.resizing-sidebar`
  during drag).
- Mode tabs styled as a segmented control; active tab gets the accent
  gradient.
- Form controls (`input`, `select`, `textarea`) share a consistent dark
  input style with focus glow.
- Buttons use a gradient background with a hover "shine sweep" effect;
  `.secondary` buttons are outlined/flat.
- Grid rendered as `table.ws-grid`:
  - Cell size/font controlled via `--cell-size` / `--cell-font` /
    `--cell-font-family` custom properties (computed in JS to fit the
    available container space).
  - `.locked` cells (pre-placed target-word letters) get a warm
    highlight; `.just-filled` cells pop in during Watch mode.
  - `table.ws-grid-play` adds hover/selection states (`.selecting`,
    `.found-cell`, `.wrong-flash`) for the Play/Collapse interactions.
  - Hex/triangular lattices offset odd rows via `transform: translateX`
    based on `--cell-size`.
- Win-state feedback: `#play-status.win` shimmer animation and a
  confetti burst layer (`.confetti-layer` / `.confetti`).
- Pause anti-cheat overlay (`.pause-overlay`): heavy blur/tint over the
  grid while paused so letters can't be read, with an icon + message.
- Collapse-mode tile drop animation via `.dropping` (`will-change:
transform`).
- Responsive: sidebar stacks above main content below 820px width.

## Follow-ups

- The `.home-link` now points to `/` (site root) instead of a relative
  `../../index.html` path — verify this resolves correctly in all
  deployment contexts (e.g. if the app is served from a sub-path,
  a relative or environment-aware path may be preferred instead).
