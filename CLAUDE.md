# Abner Wilhelm — Portfolio Site

## Overview

Single-file vanilla HTML/CSS/JS portfolio for **Abner Wilhelm**, a Technical Designer and Gameplay Programmer studying at FIEA (Florida Interactive Entertainment Academy) in Orlando. The site is hosted on GitHub Pages at `abnerwilhelm.dev` from the repo `Abner-Wilhelm/abnerwilhelm.github.io`.

**No frameworks.** No build step. Everything is in `index.html` with assets referenced by relative path from the `assets/` folder.

## File Structure

```
abnerwilhelm.github.io/
├── index.html                          (the entire site — ~230KB)
├── CLAUDE.md                           (this file)
└── assets/
    ├── Wilhelm_Resume.pdf              (downloadable from contact section)
    ├── thumbs/
    │   ├── veilwood.webm               (card thumbnail, 87KB, loops silently)
    │   └── chess-shifter.webm          (card thumbnail, 138KB, loops silently)
    └── images/
        ├── achievementmanager-addprogress-central-progress.jpg
        ├── achievementmanager-notifybugcatch-broadcasts-the.jpg
        ├── bug-interaction-handler-when.jpg
        ├── catchallbugs-cs-custom-achievementobject.jpg
        ├── catchallbugs-scriptableobject-unity-inspector.jpg
        ├── destinations-array-in-the.png       (NPC Movement Manager)
        ├── inventory-in-action-opening.gif     (1.6MB compressed from 20MB)
        ├── inventoryitemdata-inspector-defines-id.jpg
        ├── item-database-central-scriptableobject.jpg
        └── crop-1.png, crop-2.png              (edited image variants)
```

## How the Site Works

### The DEFS Array

All project content lives in a `const DEFS = [...]` JSON array near the top of the `<script>` block in `index.html`. Each element is a game/project object. The renderer reads DEFS on load, builds game cards, and renders project pages from this data.

A project object looks like:

```js
{
  "id": "g1",                    // unique ID, used in DOM + localStorage
  "title": "VeilWood",
  "studio": "Chaos Cabin LLC",
  "year": "2024",
  "role": "Technical Designer, Level Designer, Composer",
  "duration": "September 2024 – March 2026",
  "genre": "First Person Survival Farming Simulator",
  "engine": "Unity",
  "status": "In Development",
  "desc": "Project description shown below the title...",
  "tags": ["survival", "farming", "unity", "team project"],
  "thumb": "assets/thumbs/veilwood.webm",       // card thumbnail (WebM or GIF or image path)
  "bg": "#080a06", "ac": "#1a2808", "pr": "#406020",  // card colors
  "tabs": [ ... ],               // array of tab objects (or null for no-tab projects)
  "blocks": [ ... ],             // flat block array (only if tabs is null)
  "reflection": "Reflection text shown at bottom of project page..."
}
```

### Tabs vs No Tabs

- **Projects with tabs** (VeilWood, Mythbound): content lives in `project.tabs[].blocks[]`
- **Projects without tabs** (Chess Shifter): content lives in `project.blocks[]`

If a project has only 1 tab, it still uses the `tabs` array — the tab bar just doesn't render. If `tabs` is `null`, blocks render directly without any tab UI.

### Block Types

Each block in a tab's `blocks` array is one of:

| Type | Purpose | Key Fields |
|------|---------|------------|
| `subsection-header` | Major section heading (e.g. "Achievement System") | `label` |
| `sub-header` | Sub-item heading (e.g. "NPC State Management") | `label` |
| `text` | Paragraph of text | `content` (HTML string, supports `\n` for line breaks) |
| `section` | Layout container — holds columns of blocks | `layout`, `cols` |
| `media` | Image/GIF block | `files[]` (paths), `meta{}` (captions, fit, cropped), `compact` (bool) |
| `embed` | YouTube/Vimeo embed or uploaded MP4 | `url` (YouTube URL) or `file` (MP4 data) |
| `audio` | Audio player | `file`, `label` |
| `code` | Code block | `content`, `lang` |

### Section (Container) Layouts

A `section` block has a `layout` field and a `cols` array:

```js
{
  "type": "section",
  "layout": "one-two",        // "full" | "half" | "thirds" | "one-two" | "two-one"
  "cols": [
    [ {block}, {block} ],     // left column (⅓)
    [ {block}, {block} ]      // right column (⅔)
  ]
}
```

Common pattern: `sub-header` → `section` with layout `one-two`, text in `cols[0]`, embed/media in `cols[1]`.

### Media Block with Captions

```js
{
  "type": "media",
  "files": ["assets/images/screenshot.jpg", "assets/images/code.png"],
  "compact": true,             // renders as small grid, click opens lightbox
  "meta": {
    "0": { "caption": "Description of first image", "fit": "contain" },
    "1": { "caption": "Description of second image", "fit": "cover" }
  }
}
```

- `fit`: `"cover"` (fill frame, may clip) or `"contain"` (show full image, may letterbox)
- `compact`: `true` renders images in a responsive grid with small captions, `false` renders full-size stacked
- `caption`: small text below each image with a blue left border accent

### Thumbnail Format

Thumbnails use WebM video for efficiency (GIF equivalent at ~5% the file size). The card renderer detects `.webm` or `.mp4` extensions and outputs a `<video autoplay loop muted playsinline>` tag instead of `<img>`.

To add/replace a thumbnail:
1. Convert source MP4 to WebM: `ffmpeg -i input.mp4 -c:v libvpx-vp9 -b:v 0 -crf 33 -vf "scale=360:-1" -an assets/thumbs/name.webm`
2. If there's a watermark/build text to crop: add `crop=W:H:X:Y` before scale in the filter chain
3. Update the project's `"thumb"` field in DEFS to point to the new file

## Current Projects (3)

### VeilWood (`g1`)
- **Studio:** Chaos Cabin LLC | **Engine:** Unity | **Role:** Technical Designer, Level Designer, Composer
- **Duration:** September 2024 – March 2026 | **Status:** In Development
- **3 Tabs:** Programming | Level Design | Music Composition
- **Programming tab** has all descriptions filled in: Achievement System (5 screenshots in compact grid), AI State Management (NPC scheduling + Mistwalker + Crow AI with videos), Catacomb Puzzle System (Bug Shrine with 3 mini-games + Nutrient + Rotating Pillar + Flower + Water puzzles, all with YouTube URLs), Inventory System (3 images in compact grid including compressed GIF), Save/Load System
- **Level Design tab:** placeholder content (Catacombs, Town & Farm, Cavern Wilderness — needs images/text)
- **Music Composition tab:** placeholder content (needs audio files and descriptions for 10 tracks)
- **YouTube URLs placed:** Bug Shrine (TqeCDn0Qs6w), Pachinko (HIKRBmFuM_M), Slot Machine (PTP1WotasYw), Blackjack (oGAKkm2cHIA), Nutrient (YqsyqPJR9-0), Rotating Pillar (QwK3EN-PCgc), Flower (28A-ei-oeEg), Water (cCbRy7U3L8A), NPC Navigation (z0ll4uiCpHE), Mistwalker AI (7rYbvmF-cCc), Crow AI (2pfkv510zeI)

### Mythbound (`g-mythbound`)
- **Studio:** Skaldforge | **Engine:** Unreal Engine 5.7 | **Role:** Lead Technical Designer
- **Duration:** December 2025 – Present | **Status:** In Development
- **2 Tabs:** Programming | Design
- **Programming tab** descriptions are filled in from resume: GAS base class, Ground Slam (Air + Ground), Grapple, Lightning/Alvar/Dagger Projectiles, Wind Charge, Hex Teleport, Applied Effects, Networked Sound, Team Respawn, Objective (Scoring/Waypoints/Throwing)
- **Design tab:** 5 Pillars intro written but individual pillars + characters are placeholder (needs pillar names and character names/descriptions)
- **Needs:** 8 ability videos, Base Class image, Mythbound thumbnail, pillar details, character details

### Chess Shifter (`g-chess`)
- **Studio:** Solo — FIEA | **Engine:** Unity | **Role:** Technical Designer
- **Duration:** November 2025 – December 2025 | **Status:** Released
- **No tabs** — flat block list
- **All sections written:** Piece Data, Movement System, Enemy AI, Shifting Tiles, Totems, Inventory, Puzzle Design
- **Won "Best Overall Game" at FIEA** — mentioned in description and Puzzle Design section
- **Needs:** more screenshots/media for each section

## Design Constants

### CSS Variables (defined in `:root`)
```css
--bg: #090909;
--accent: #5fb4d8;          /* light blue — primary accent */
--accent2: #3a7a96;          /* darker blue — secondary accent, borders */
--white: #ffffff;
--text: #e8e8e8;             /* main body text */
--muted: #c4c4c4;            /* secondary text, captions */
--border: #1e1e1e;
--border2: #2a2a2a;
```

### Fonts
- **Display:** Cormorant Garamond (`var(--ff-d)`) — headings, hero, body text
- **Mono:** Space Mono (`var(--ff-m)`) — labels, buttons, captions, code, UI elements

### Visual Design Notes
- Dark atmospheric aesthetic with light blue accent
- Custom crosshair cursor with trailing dot (disabled on mobile via media query)
- Ember spark particles on cursor move
- Card hover reveals "View project" overlay
- Subsection headers get a small blue bar above them and an accent underline
- ⅓+⅔ layout containers get a subtle blue gradient background and border in view mode
- Video embeds get drop shadow framing in view mode
- Compact image grids: responsive columns with small captions, click opens lightbox

## Edit Mode

`EDIT_MODE_ENABLED` is a constant at the top of the `<script>` block. Currently set to `true`.

When `true`: shows the edit bar (top of page), sidebar (⊞ Blocks button), block toolbars (move/delete/duplicate buttons on hover), insert (+) buttons between blocks, container header bars with layout switcher, height controls, image editor, and all contenteditable attributes are active.

When `false`: all edit UI is hidden via injected CSS. The site looks clean and public-ready.

To switch: change `const EDIT_MODE_ENABLED = true;` to `false` (or set it to a URL param check like `new URLSearchParams(window.location.search).get('edit') !== null` to unlock via `?edit`).

## localStorage

The site saves edited game data to `localStorage` key `pf_games2` as JSON. This overrides DEFS on load. If the saved data has fewer projects than DEFS, it falls back to DEFS automatically.

**Tech stack** is saved separately in `pf_tech`. **Accent color** in `pf_accent`.

**To force a reset:** `localStorage.clear(); location.reload();` in the browser console, or click "Reset all" in the edit bar.

**Important:** When editing the DEFS in `index.html` directly, the user needs to clear localStorage in their browser to see the changes (since localStorage takes priority over DEFS on load).

## Common Tasks

### Adding a new image to a section
1. Put the image file in `assets/images/` with a descriptive kebab-case name
2. Find the relevant section in DEFS → find the `media` block in the appropriate column
3. Add the file path to the `files` array
4. Add a caption entry in `meta` with the file index as the key

### Adding a YouTube video to a section
1. Find the `embed` block in the section's column (usually `cols[1]` in a `one-two` layout)
2. Set `"url": "https://youtu.be/VIDEO_ID"`

### Adding a new project
1. Add a new object to the DEFS array following the existing structure
2. If the project has multiple content areas, use `tabs` with tab objects
3. If it's a simple project, use `blocks` directly and set `tabs` to `null`
4. Add a thumbnail to `assets/thumbs/` and reference it in `"thumb"`

### Converting MP4 to WebM for thumbnails
```bash
ffmpeg -i input.mp4 -c:v libvpx-vp9 -b:v 0 -crf 33 -vf "scale=360:-1" -an assets/thumbs/name.webm
```
To crop out watermarks/build text first:
```bash
ffmpeg -i input.mp4 -c:v libvpx-vp9 -b:v 0 -crf 33 -vf "crop=1173:660:0:0,scale=360:-1" -an assets/thumbs/name.webm
```

### Compressing a GIF for inline use
```bash
# Generate palette
ffmpeg -i input.gif -vf "fps=12,scale=600:-1:flags=lanczos,palettegen=stats_mode=diff" palette.png
# Apply palette
ffmpeg -i input.gif -i palette.png -filter_complex "fps=12,scale=600:-1:flags=lanczos[x];[x][1:v]paletteuse=dither=bayer:bayer_scale=5" output.gif
```

### Deploying
```bash
git add .
git commit -m "description of changes"
git push
```
Site updates in ~30 seconds at `abnerwilhelm.dev`.

## Abner's Preferences

- **Preserve existing code structure** when making changes — don't reorganize or rename things without being asked
- **Prefer scalable, modular systems** and expandable architectures
- **Prioritize gameplay systems and technical design** discussions over UI implementation
- **Keep save/load centralized** through managers rather than spreading logic across classes
- **Strategy-heavy, systems-driven gameplay design** is his interest area
- **Owns all his music compositions** — respect ownership boundaries
- Prefers "player choice and enemy variability" over "tactical depth" when describing ChessShifter
- **Dislikes being called "the UI guy"** even though he's competent at it
- Written content should be **concise and technical** — he writes like a designer talking to other designers, not marketing copy

## What Still Needs Content

### VeilWood
- Level Design tab: images and descriptions for Catacombs, Town & Farm, Cavern Wilderness
- Music Composition tab: 10 audio tracks with descriptions
- VeilWood project banner image (21:6 ratio, top of project page)

### Mythbound
- Thumbnail (no footage yet)
- 8 ability demo videos (Ground Slam Air/Ground, Grapple, Lightning/Alvar/Dagger Projectile, Wind Charge, Hex Teleport)
- 1 image for Projectile Base Class
- Design tab: 5 pillar names and descriptions, character names and ability kit descriptions
- Project banner image

### Chess Shifter
- Screenshots/media for each section (Piece Data, Movement, AI, Shifting Tiles, Totems, Inventory, Puzzle Design)
- Project banner image

### General
- Real social media URLs (currently `href="#"` placeholders on contact links)
- Hero section could use a background video eventually (infrastructure was removed but can be re-added)
