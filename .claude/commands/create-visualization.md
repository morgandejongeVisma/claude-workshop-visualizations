# Create Visualization

You are creating a new interactive visualization for Idella's Claude/AI workshop series. The user will describe the concept they want to visualize. Your job is to design and build it as a single self-contained HTML file.

## Input

$ARGUMENTS — The concept to visualize and any specific requirements.

## Process

### 1. Understand the concept

Before writing any code, think carefully about:
- What is the core insight the presenter wants the audience to understand?
- What is the best way to **show** this concept visually? (Not explain it with text — show it with animation, diagrams, charts, movement.)
- How many steps does this need? Aim for the minimum number of steps that effectively communicates the concept. Typically 3-6 steps. Each step should add meaningful new information.
- What visual metaphors or representations will make this intuitive?

### 2. Design the layout

Each visualization is unique — don't force a fixed layout. Choose the arrangement that best serves the concept:
- Side-by-side panels (e.g., input vs output, before vs after)
- A single large diagram that builds up incrementally
- Multiple coordinated views (e.g., a simulation + a chart tracking a metric)
- A flow/pipeline that elements move through
- Any other layout that fits the concept

### 3. Build the visualization

Create the file at `visualizations/<slug>/index.html` where `<slug>` is a short kebab-case name for the concept.

Read the project's CLAUDE.md for the full design system and boilerplate. The key rules are summarized below.

## Critical design principles

**This is NOT a slideshow.** This is the single most important principle:
- The visualization is a **single persistent scene** that evolves as the presenter steps through it
- Each step **adds to** or **transforms** what's already on screen — it does NOT clear and replace content
- There should be no step titles that change, no "slide 1 / slide 2" feeling
- No "key takeaways" or summary steps — the presenter handles that verbally or in their slide deck
- Think of it like an interactive animation that the presenter controls, not a set of slides

**Show, don't tell:**
- Prefer visual elements (charts, diagrams, animations, simulations) over text
- Text labels and short annotations are fine; paragraphs of explanation are not
- Use animation to show processes, transitions, and cause-effect relationships
- The presenter will explain what's happening — the visualization just needs to SHOW it

**Keep it focused:**
- Each visualization explains ONE concept
- Minimize the number of steps — only as many as needed
- Every step should add something visually meaningful

## Visual style (mandatory)

These must be followed exactly. Read CLAUDE.md for the full specification.

**Colors:**
- `--color-bg: #f5f2eb` — light background / content card
- `--color-text: #000000` — text on light background
- `--color-text-inv: #ffffff` — text on accent colors
- `--color-accent1: #d97757` — primary accent
- `--color-accent2: #cc9b7a` — secondary accent
- `--color-accent3: #e9b19f` — tertiary accent

**Fonts:** PT Serif Bold (headings), Montserrat 400/600 (body) — loaded via Google Fonts with `&display=swap`.

**Layout frame:**
- Body background `#000` (letterboxing)
- `.viewport` sized to 16:9 via JS, background image `../../background/background.png`
- `.content-card` cream rounded rectangle on top of the background (where all content lives)
- Idella logo is baked into the background — do not add a separate logo
- Navigation bar at bottom center of viewport (prev/next buttons + step indicator)

**Sizing:** All dimensions use `calc(var(--vw) * N)` where `--vw` is set by JS to 1% of viewport width. This ensures everything scales proportionally.

**Animation:** Use GSAP 3.12 via CDN. Animate new elements appearing, bars growing, objects moving, etc. Keep animations snappy (0.3-0.8s typically).

## Boilerplate structure

Every visualization must include this skeleton (see CLAUDE.md for the full template):

```
<head>
  - Google Fonts (PT Serif 700, Montserrat 400;600)
  - GSAP CDN (and any other CDN libraries needed)
  - All CSS inline in <style>
</head>
<body>
  <div class="viewport" id="viewport">
    <div class="content-card">
      <!-- Your visualization content here -->
    </div>
    <div class="nav-bar">
      <!-- prev/next buttons + indicator -->
    </div>
  </div>
  <script>
    // Viewport 16:9 sizing
    // Step navigation (arrow keys + buttons)
    // Your visualization logic
  </script>
</body>
```

The navigation pattern:
- Track `currentStep` and `totalSteps`
- `goToStep(n)` function that calls `renderUpToStep(n, animate)`
- `renderUpToStep` rebuilds state to match the target step (so going backward works correctly), and optionally animates the latest addition when going forward
- Arrow keys (Left/Right) and button click handlers

## Reference implementation

Study `visualizations/context-window/index.html` for a concrete example of how all these principles come together. Note how:
- It uses a persistent two-column layout (chat + chart) that never clears
- Each step adds a chat exchange AND grows a bar in the chart simultaneously
- The bar chart makes the growing token count visually obvious without needing text explanation
- `renderUpToStep()` can rebuild any state for backward navigation
- Animations only fire when stepping forward

## After creating the file

1. Test it by opening the file in a browser
2. Add a card linking to it in the root `index.html`
3. Confirm the visualization works with arrow keys and nav buttons
4. Verify it renders correctly in fullscreen (F11)

## Deployment

The repo deploys to GitHub Pages automatically on push to `main`.

- **Repo**: `morgandejongeVisma/claude-workshop-visualizations`
- **Live URL**: https://morgandejongevisma.github.io/claude-workshop-visualizations/
- **New visualization URL**: `https://morgandejongevisma.github.io/claude-workshop-visualizations/visualizations/<slug>/`

After testing locally, commit the new files and push. The site updates within ~30 seconds. Note: `.claude/` and `CLAUDE.md` are gitignored — only visualization files and assets are deployed.
