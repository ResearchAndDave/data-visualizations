# AGENTS.md — football-injuries

Guidance for AI coding agents working on the **football-injuries** visualization in this portfolio. Human contributors may find it useful too. This file applies to everything under `football-injuries/`.

## What this is

A single-page **data-journalism dashboard** analyzing professional soccer injuries by playing surface ("The Pitch Report"). The entire app is one self-contained file — `index.html` — with inline CSS and vanilla JS. **No build step, no framework, no npm dependencies.** It renders by manipulating the DOM directly. This directory is the published/deployed copy.

## Layout

| Path | Purpose |
|------|---------|
| `football-injuries/index.html` | The whole app: inline CSS + vanilla JS. Edit this for any UI/logic change. |
| `football-injuries/data.json` | Real injury dataset (API-Football export). Auto-loaded by the page via `fetch('data.json')`. |

## Running & verifying

There is no test suite. Verify changes by running the app in a browser.

```bash
# Serve locally (the page fetches data.json, so file:// will not work)
cd football-injuries && python3 -m http.server 8765
# then open http://localhost:8765/index.html
```

Append a cache-busting query (`?v=2`) when reloading after edits.

**Syntax-check the inline JS without a browser:**

```bash
node -e "const fs=require('fs');const m=fs.readFileSync('football-injuries/index.html','utf8').match(/<script>([\s\S]*)<\/script>/);try{new Function(m[1]);console.log('JS OK');}catch(e){console.log('ERR',e.message);}"
```

**Always confirm a visual/behavioral change by actually loading the page** (e.g. via a headless browser / screenshot) and checking the affected section — not just by reading the diff. The app auto-loads `data.json` on startup; after load, `state.injuries.length` should be **3823** (4004 raw records minus illnesses, after normalization).

## Architecture / code map (`index.html`)

- **State**: a single global `state` object (`injuries`, `source`, `gamesBySurface`, `filters`). `render()` tears down `document.body` and rebuilds it from `state` on every change.
- **Data loading**: `loadFromURL('data.json')` (auto-load on startup) and `loadJSON(file)` (manual upload) both route raw records through **`mapInjuryRecords()`**, so cleaning applies no matter how data arrives. A loading state is shown until `data.json` resolves so raw/synthetic data never flashes.
- **Injury normalization**: `normalizeInjuryType(raw)` maps the source's ~128 messy labels into a clean category set (muscle locations roll up into `Muscle Injury`; junk placeholders become `Unspecified`; illnesses return `null` and are dropped; casing/spelling variants merge). If you touch categories, update this function and re-verify the counts.
- **Render pipeline** (`render()` calls, in order): filter bar + section nav → masthead → source banner → headline → key findings → `renderSurfaceComparison` → `renderCallout` → `renderInjuryTypes` → `renderTeamRankings` → `renderPlayerLog` → footer.
- **Section ids** for the jump-nav: `#top`, `#surfaces`, `#mix`, `#teams`, `#players`.

## Domain rules — do not regress these

These reflect deliberate analytical decisions. Preserve them:

1. **Surfaces have no single "worst."** Present raw count and per-game rate side by side (`renderSurfaceComparison`); never crown one surface from raw counts (raw counts mostly reflect how many games a surface hosts, not risk).
2. **Exposure matters.** Per-game rate = injuries ÷ `gamesBySurface`. Surfaces with fewer than `LOW_GAME_THRESHOLD` (300) games are shown with a hatched bar to signal low confidence.
3. **The injury mix is consistent across surfaces.** `renderInjuryTypes` shows each type as a share of its own surface (not raw counts) so the bars line up and demonstrate this.
4. **`daysOut` is empty in the source** (all zeros) — do not build "time lost / recovery" features on it without new data; the footer says as much.

## Data

`data.json` is the canonical dataset (an API-Football export). `index.html` fetches it by that exact filename. If you regenerate it, keep the filename `data.json` and re-verify the post-normalization count (3823).

## Conventions

- **No new dependencies or build tooling** — keep `index.html` self-contained and vanilla.
- **Sign commits** (`git commit -S`). Imperative subject, short body explaining *why*.
- **Verify SHAs after editing**: `git hash-object football-injuries/index.html`.

### Design standards (inline)

Visual changes must follow these (derived from Refactoring UI + Tufte):

- **Color**: every color value uses `hsl()`/`hsla()` — no hex, rgb, or named colors. Define shades as CSS custom properties; reserve the terracotta accent for genuine findings, neutrals elsewhere. Meet WCAG contrast; never use color as the sole indicator (pair with text/icon/texture).
- **Spacing**: only 4, 8, 12, 16, 24, 32, 48, 64, 96, 128 px. More space between groups than within them.
- **Type sizes**: only 12, 14, 16, 18, 20, 24, 30, 36, 48, 60, 72 px. Proportional line-height (body 1.5–2.0, headings 1.0–1.25, labels 1.0).
- **Charts (Tufte)**: maximize data-ink — no gridlines/chartjunk, direct labels over legends, restrained palette. Show comparisons adjacently; encode uncertainty; don't let the most fragile number be the most prominent.
