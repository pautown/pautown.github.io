# Artistic Direction Guide

## The Feeling First

Before any specification, ask: *does this feel like breath on glass?*

townhaus exists in the space between stimulus and response. It is the pause before speaking, the weight of a room when you enter it alone, the temperature of stone that remembers who touched it last.

---

## I. Color Philosophy

### The Palette (Technical)

| Token | Hex | Role |
|-------|-----|------|
| `--amber` | `#d4a056` | Primary warmth, points of light, earned attention |
| `--amber-glow` | `#e8b86d` | Hover states, active presence, breath |
| `--amber-deep` | `#8b6914` | Grounding, density, age |
| `--slate` | `#4a5568` | Text, structure, the container |
| `--slate-deep` | `#2d3748` | Emphasis without shouting |
| `--bone` | `#f5f0e6` | Primary ground, paper quality, silence |
| `--paper` | `#faf6ed` | Elevated surfaces, lightness |
| `--ink` | `#2d2a24` | Words that matter |
| `--green-orb` | `#86b398` | Strategic accent, life, patience |
| `--green-deep` | `#4a7c59` | Forest floor, depth |

### The Palette (Sensory)

**Amber** is lamplight through curtains at 4pm in autumn. Not gold (too triumphant), not orange (too urgent). It is the color of things that have aged well and kept their warmth.

**Slate** is the inside of a shell, the wet stone of a riverbed. It holds space without filling it. Text in slate does not demand; it waits to be read.

**Bone** is older than white. It is not the blank page but the page that has been touched, set down, returned to. It knows something.

**Green** appears like moss on a circuit board - nature finding its way back. Use sparingly. When green appears, something is alive that wasn't before.

### Color as Feeling

```
This:
  A room lit by one warm lamp, the corners in shadow,
  the shadows not threatening but containing possibility.

Not this:
  A room with overhead lights, every corner visible,
  everything known and therefore nothing to discover.
```

---

## II. Typography

### Technical Specifications

**Primary (Content):**
- Font: Cormorant Garamond
- Weights: 300 (primary), 400 (rare emphasis)
- Base size: 18px (1rem)
- Line height: 1.6
- Letter-spacing: 0.02-0.05em for body, up to 0.4em for titles

**Secondary (Hints/Technical):**
- Font: JetBrains Mono
- Weights: 300, 400
- Size: 0.55-0.7rem for hints, 0.45rem for whispers
- Letter-spacing: 0.1-0.15em
- All-caps for edge hints

### Typography as Breath

Cormorant Garamond at weight 300 does not stand on the page. It rests there. The thin strokes allow the paper to breathe through the letters.

JetBrains Mono is the voice of the house itself - smaller, quieter, appearing at edges and in pauses. It is instruction that does not instruct, hints that prefer to be ignored.

### Examples

```
This:
  "the silence was always here"
  (Set in Cormorant, weight 300, centered, opacity 0.6,
   appearing after 7 seconds of stillness)

Not this:
  "THE SILENCE WAS ALWAYS HERE"
  (Shouting defeats its own message)

This:
  "move to disturb. be still to listen."
  (JetBrains Mono, 0.55rem, opacity 0.4,
   appearing only after instruction is needed)

Not this:
  "Instructions: Move your mouse to create ripples.
   Stop moving to see messages appear."
  (Explanation kills mystery)
```

---

## III. Animation & Timing

### Technical Specifications

| Duration | Use Case | Feel |
|----------|----------|------|
| 0.3-0.5s | Micro-interactions (hover) | Responsive but unhurried |
| 1.5-2s | State transitions | Deliberate |
| 3-4s | Reveals, arrivals | Patient |
| 4-6s | Breathing, pulsing | Biological |
| 8-20s | Drifting, ambient | Environmental |

**Easing:** `cubic-bezier(0.4, 0, 0.2, 1)` for most transitions. This curve is patient at the start and gentle at the end.

### Animation as Living

All movement in townhaus is organic. Nothing snaps into place. Nothing bounces. Nothing overshoots.

```css
/* This: */
animation: breathe 6s ease-in-out infinite;
@keyframes breathe {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.02); }
}

/* Not this: */
animation: pulse 0.5s ease infinite;
@keyframes pulse {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.2); }
}
```

The first is breathing. The second is a heartbeat in panic.

### Stillness as Animation

Stillness is not the absence of animation. Many townhaus pages reward stillness:

- **silence.html**: Messages appear after 7 seconds of no movement
- **orb.html**: The orb warms only when held
- **between.html**: Hidden messages reveal after 8 seconds in the threshold

The instruction for stillness is *never explicit*. Users discover that patience is rewarded.

---

## IV. Spacing & Layout

### Technical Grid

- Base unit: 1rem (18px)
- Generous margins: 2rem minimum from edges
- Content width: Max 1200px, often narrower
- Vertical rhythm: 1.6 line-height, margins in multiples of 0.5rem

### The Principle of Centered Asymmetry

Content gravitates toward center but is not imprisoned there. Elements drift. The grid suggests rather than enforces.

```
This:
  .element {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    /* But then: subtle drift via animation */
  }

Not this:
  .element {
    display: grid;
    place-items: center;
    /* Locked in place, rigid */
  }
```

### Whitespace as Content

The space around an element is not empty. It is the silence that gives the word meaning. In townhaus:

- An element alone on the page is complete
- Crowding is never necessary
- If it feels cramped, remove something

---

## V. Sensory Vocabulary

These are the physical experiences townhaus evokes. Reference them when making decisions.

### Breath on Glass
Temporary. Evidence of presence. Fading but real.
- Elements that appear and fade
- Opacity never 1 (except strategically)
- Presence leaves traces

### Fog Lifting Slowly
Revelation through patience. What was obscured becomes clear, but slowly.
- Information unfolds over time
- Scrolling or waiting reveals
- Nothing is immediate

### Stone Warming to Touch
The environment responds to presence.
- Hover states that accumulate
- Elements that "remember" interaction
- Temperature as metaphor (cold/warming/warm)

### Moss on Circuit Boards
Nature reclaiming technology. The organic reasserting itself.
- Soft edges on hard grids
- Green appearing in technical contexts
- Imperfection as intention

---

## VI. Physical Space Analogies

When designing a new page, ask: *which space is this?*

### Forest Clearing
A cathedral of trees, a single shaft of light.
- **forest.html** embodies this
- Vertical orientation
- Depth through layering
- Light as rare and precious

### Overgrown Greenhouse
Plants claiming glass and metal. Structure visible beneath growth.
- Gridded elements with organic intrusions
- Transparency and layers
- The sense of something tended, then left

### Norwegian Cabin
Warm inside, snowy darkness outside. Containment as comfort.
- Defined edges around warm interiors
- The outside exists but is not entered
- **silence.html** in "deep silence" mode

---

## VII. Sound Philosophy

Sound in townhaus is:
- Optional, never default
- Hidden but findable
- Related to something that doesn't seem like sound

Each page might have:
- A sound possibility (not obligation)
- Something that *feels* like sound even if silent
- The suggestion that sound could exist here

Examples:
- **switch.html**: The mechanical click is felt, not heard
- **silence.html**: The absence of sound *is* the sound
- **forest.html**: Birds visually, sound implied

The philosophy: slightly obvious but satisfying to find.

---

## VIII. Coherence Rules for New Pages

### Same Spirit, Different Form

A new page can look nothing like existing pages if it *feels* like townhaus. Ask:
- Does it reward patience?
- Does it keep secrets?
- Does it breathe?

### Surprising but Fitting

Surprise is welcome. Confusion is not. A user should feel "oh, of course" not "what is this?"

```
This:
  A page that is entirely black until you hover,
  then reveals a single word.
  (Surprising, but fitting)

Not this:
  A page that plays loud music and has blinking text.
  (Surprising, but violates everything)
```

### Deliberate Disruption

Rules can be broken to highlight them. If every page is calm, one page might be intentionally jarring - but the jarring quality makes a point about calmness.

The **evil/beauty** flip tile breaks visual rules. It contains deep burgundy and dramatic effects. This works because it is contained, intentional, and ultimately leads to a question.

---

## IX. Anti-Patterns

Things townhaus is not:

### No Urgency/FOMO
- No countdown timers
- No "limited time" language
- No notifications
- Nothing expires

### No Tutorialization
- No onboarding
- No tooltips
- No "click here to begin"
- Discovery is the only teacher

### No Brightness/Loudness
- No pure white backgrounds
- No full opacity elements (except strategically)
- No large bold text
- No exclamation points (except "switch.html" glyph, ironically)

### No Completion/Closure
- No "you have completed X"
- No progress bars
- No achievements
- Everything is ongoing

### But: Avoid Avoiding Anything

These rules breathe. The switch page uses an exclamation point as its glyph - precisely because the switch does nothing. The evil tile is loud - because it exists in tension with beauty.

Rules are structure, not prison. When you break a rule, know you are breaking it and why.

---

## X. Duration Philosophy

All durations are valid.

- 30 seconds on a page is complete
- 3 hours on a page is complete
- Opening a page and closing it immediately is complete
- Returning to a page months later is complete

townhaus does not measure engagement. It does not optimize for time-on-page. It exists whether or not anyone is looking.

---

## XI. Hidden Things

Every page contains something hidden. Some are documented here:

| Page | Secret | Trigger |
|------|--------|---------|
| silence.html | "YOU FOUND THE QUIET" | Arrow sequence: up up down down |
| orb.html | Prismatic mode | Hold orb for exactly 10 seconds |
| switch.html | Visual changes | 100, 500, 1000 clicks |
| forest.html | All secrets revealed | Arrow sequence + 5 mushrooms |
| between.html | Hidden truth | Dwell in expanded state 8+ seconds |

But this is not a checklist. Users are not expected to find everything. The secrets exist whether or not they are discovered.

---

## XII. Console as Space

The browser console is another room in the house. Each page speaks to it:

```javascript
console.log('%csilence', 'font-size: 20px; color: #4a5568; font-weight: 100; letter-spacing: 0.5em;');
console.log('%cis louder than you think', 'font-size: 10px; color: #718096; font-style: italic;');
```

Console messages are:
- Styled consistently with page aesthetics
- Poetic, not instructional
- Another layer of discovery

---

## XIII. Reference Points

These influences are implicit, not cited. The work should feel informed by them without naming them.

### Visual
- Fog Index: Atmospheric uncertainty
- Slow cinema: Duration as meaning
- Sumi-e: The importance of empty space

### Interactive
- Mystery games without solutions
- Environments that respond to presence
- The feeling of an empty house that is not quite empty

### Natural
- Lichen growth patterns
- The weight of humid air before rain
- Tide pools at rest

---

## XIV. Practical Checklist

Before a page is added to townhaus:

1. [ ] Does it load quietly? (No loud initial states)
2. [ ] Does it reward stillness somewhere?
3. [ ] Does it have at least one hidden thing?
4. [ ] Is there a path back? (Return link present)
5. [ ] Does the console say something?
6. [ ] Can it be experienced in 30 seconds or 30 minutes?
7. [ ] Does it feel like the other rooms in this house?

---

## XV. Final Note

This guide is a map, not the territory. townhaus is felt before it is understood. If you find yourself referring to this document constantly, step back. Close the specifications. Sit with silence.html for five minutes without moving.

Then design.

---

*The best way to understand townhaus is to spend time there.*

