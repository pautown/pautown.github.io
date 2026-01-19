# CLAUDE.md - townhaus

This file provides guidance to Claude Code when working with this repository.

## Project Overview

**townhaus** is an interactive web art space featuring a generative grid interface with contemplative design. It serves as the personal portfolio and creative showcase hosted at pautown.github.io, including pages for llizardOS and related projects.

**Repository:** github.com/pautown/pautown.github.io
**Live Site:** https://pautown.github.io

## Structure

```
townhaus/
├── index.html           # Main landing page with fragment grid
├── shared.css           # Shared aesthetic system (colors, animations)
├── pages/               # Individual fragment pages
│   ├── almost.html      # Various themed interactive pages
│   ├── barn.html
│   ├── between.html
│   ├── clocks.html
│   ├── door.html
│   ├── forest.html
│   ├── jungle.html
│   ├── mirrors.html
│   ├── mystery.html
│   ├── office.html
│   ├── orb.html
│   ├── quantum.html
│   ├── silence.html
│   └── projects/        # Project showcase pages
│       ├── index.html
│       ├── llizardos.html   # llizardOS project page
│       ├── janus.html
│       └── mercury.html
└── docs/
    ├── vibe.md          # Design philosophy and aesthetic guide
    ├── ethos.md         # Project ethos
    ├── artistic-direction.md
    └── design-tokens.yaml
```

## Key Features

### Interactive Elements
- **Draggable fragment tiles** - Grid of clickable tiles that can be rearranged
- **localStorage persistence** - Remembers tile arrangement across visits
- **Keyboard shortcuts**: `[b]` breathing animation, `[d]` dark mode, `[s]` scatter tiles
- **Right-click interactions** - Contextual ambient messages
- **Stillness detection** - After 30s inactivity, vines grow from corners

### Atmospheric Effects
- Grain texture overlay (static SVG filter)
- Floating amber particles with staggered animations
- Mist gradient at viewport bottom
- "Presence" orbs that appear during user stillness
- Edge hints that reveal gradually

### Music Footer (Aesthetic Only)
- Non-functional player displaying fictional track titles
- Winamp/Windows XP aesthetic
- Track names like "clouds on a sunny day", "the sound of almost remembering"

## Design Philosophy

From `docs/vibe.md`:

- **Gentle absurdism** - The lightswitch that does nothing is the perfect interaction
- **Silence that speaks** - The more nothing happens, the more is communicated
- **Mystery as hospitality** - Mystery is an invitation to stay, not a puzzle to solve
- **Paradox as peace** - Contradictions are places to rest, not problems to solve

### Color Palette (from shared.css)
- `--amber: #d4a056` - warmth, attention, revelation
- `--slate: #5a5a5a` - wisdom, mystery, background
- `--bone: #f5f0e6` - the space itself
- `--ink: #2a2a2a` - text, presence, weight
- `--green: #6b8e6b` - life, growth

### Typography
- **Cormorant Garamond** - Poetry, messages, presence
- **JetBrains Mono** - Hints, counters, whispers

## Page Template

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>[name] - townhaus</title>
    <link rel="stylesheet" href="../shared.css">
    <style>/* page-specific styles */</style>
</head>
<body>
    <a href="../index.html" class="back-link">return</a>
    <!-- page content -->
    <script>/* if interactivity needed */</script>
</body>
</html>
```

## Development

This is a static site with no build process. To develop locally:

```bash
# Serve locally (any simple HTTP server)
python3 -m http.server 8000
# or
npx serve .

# Open in browser
open http://localhost:8000
```

## Relation to llizardgui-host

The `pages/projects/llizardos.html` page showcases the llizardOS project, which is the firmware that runs llizardgui-host on CarThing devices. This repository serves as the public-facing documentation and portfolio site.
