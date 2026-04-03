# Claude Visualizations

Single-page interactive animated simulations that visualize AI and Claude concepts for Idella's "Claude Hands-On Workshop" series. Each visualization is a self-contained HTML file designed for live presentation — open a URL, go fullscreen, and step through the animation with arrow keys or on-screen buttons.

These are **not slideshows**. They are interactive visual simulations that complement a Google Slides deck. The presenter switches from slides to a visualization URL to demonstrate a concept visually, then switches back. Text-heavy explanations, titles that change per step, and "key takeaways" belong in the slide deck, not here.

## Project Structure

```
claude-visualizations/
├── CLAUDE.md
├── index.html                              # Landing page listing all visualizations
├── background/
│   └── background.png                      # Shared background image (includes Idella logo)
├── idella-logos/
│   ├── Idella_Logo_Mono_Whitex1.png
│   └── Idella_Logo_FullColor_DarkBGx1.png
├── .claude/
│   └── commands/
│       └── create-visualization.md         # Skill for creating new visualizations
└── visualizations/
    └── <slug>/
        └── index.html                      # One self-contained file per visualization
```

## Design System

### Colors

| Token              | Hex       | Usage                          |
|--------------------|-----------|--------------------------------|
| `--color-bg`       | `#f5f2eb` | Light background / content card |
| `--color-text`     | `#000000` | Text on light background        |
| `--color-text-inv` | `#ffffff` | Text on accent colors           |
| `--color-accent1`  | `#d97757` | Primary accent (buttons, highlights) |
| `--color-accent2`  | `#cc9b7a` | Secondary accent                |
| `--color-accent3`  | `#e9b19f` | Tertiary accent / subtle fills  |

### Fonts

- **Headings**: PT Serif Bold (700) — Google Fonts
- **Body text**: Montserrat Regular (400) and SemiBold (600) — Google Fonts
- Load with `&display=swap` to avoid invisible text during load

### Layout

- **16:9 aspect ratio viewport** centered on screen, sized to fill the browser window
- **Black letterboxing** (`body` background is `#000`) when the browser isn't exactly 16:9
- **Background image** (`background.png`) fills the viewport as a frame
- **Content card**: A cream `#f5f2eb` rounded rectangle sits on top of the background — this is where all content lives (matching the Google Slides aesthetic)
- **Idella logo**: Already baked into `background.png` — no separate overlay needed
- **Font sizing**: Use the `--vw` CSS custom property (set by JS to 1% of viewport width) so all text scales proportionally. Use `calc(var(--vw) * N)` for all dimensions.

### Navigation

- **Bottom-center nav bar** inside the viewport (between content card and viewport bottom edge) with prev/next arrow buttons and step indicator ("3 / 5")
- **Arrow keys**: Left = previous step, Right = next step
- **No auto-play** — all advancement is manual
- **Smooth transitions** between steps using CSS transitions or GSAP

## Core Design Principles

### Single persistent scene
Each visualization is ONE scene that evolves. Steps add to or transform what's on screen — they never clear and replace it. The audience should see a continuous visual narrative, not a sequence of slides.

### Show, don't tell
Prefer charts, diagrams, animations, and simulations over text. Labels and short annotations are fine; paragraphs are not. The presenter explains verbally — the visualization just needs to demonstrate the concept visually.

### Minimal steps
Use the fewest steps needed to communicate the concept. Typically 3-6. Each step must add something visually meaningful.

### No slideshow patterns
- No per-step titles or headings that change
- No "key takeaways" or summary steps
- No clearing the screen between steps
- No step-by-step text explanations

## HTML Boilerplate

Every visualization shares this skeleton. The content and layout inside `.content-card` varies per visualization — choose whatever arrangement best serves the concept.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title><!-- Visualization Title --> — Claude Workshop</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@400;600&family=PT+Serif:wght@700&display=swap" rel="stylesheet">
  <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/gsap.min.js"></script>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    :root {
      --color-bg: #f5f2eb;
      --color-text: #000000;
      --color-text-inv: #ffffff;
      --color-accent1: #d97757;
      --color-accent2: #cc9b7a;
      --color-accent3: #e9b19f;
      --vw: 1vw;
    }

    body {
      background: #000;
      display: flex;
      align-items: center;
      justify-content: center;
      width: 100vw;
      height: 100vh;
      overflow: hidden;
      font-family: 'Montserrat', sans-serif;
      font-weight: 400;
      color: var(--color-text);
    }

    .viewport {
      position: relative;
      background-image: url('../../background/background.png');
      background-size: cover;
      background-position: center;
      overflow: hidden;
    }

    .content-card {
      position: absolute;
      top: 4%;
      left: 3%;
      right: 3%;
      bottom: 12%;
      background: var(--color-bg);
      border-radius: calc(var(--vw) * 1.5);
      overflow: hidden;
      padding: calc(var(--vw) * 1.5);
      /* Use flexbox to arrange your layout inside */
    }

    h1, h2, h3 {
      font-family: 'PT Serif', serif;
      font-weight: 700;
    }

    /* Navigation bar */
    .nav-bar {
      position: absolute;
      bottom: calc(var(--vw) * 1.2);
      left: 50%;
      transform: translateX(-50%);
      display: flex;
      align-items: center;
      gap: calc(var(--vw) * 1.5);
      z-index: 100;
    }

    .nav-btn {
      background: rgba(255, 255, 255, 0.25);
      border: none;
      color: var(--color-text-inv);
      font-size: calc(var(--vw) * 1.8);
      width: calc(var(--vw) * 3.5);
      height: calc(var(--vw) * 3.5);
      border-radius: 50%;
      cursor: pointer;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: background 0.2s;
      font-family: 'Montserrat', sans-serif;
    }

    .nav-btn:hover { background: rgba(255, 255, 255, 0.4); }
    .nav-btn:disabled { opacity: 0.3; cursor: default; }
    .nav-btn:disabled:hover { background: rgba(255, 255, 255, 0.25); }

    .nav-indicator {
      color: var(--color-text-inv);
      font-size: calc(var(--vw) * 1.4);
      font-weight: 600;
      min-width: calc(var(--vw) * 6);
      text-align: center;
      text-shadow: 0 1px 3px rgba(0,0,0,0.3);
    }

    /* ── Waffle Menu ── */
    .waffle-menu {
      position: absolute;
      top: calc(var(--vw) * 0.6);
      left: calc(var(--vw) * 1);
      z-index: 200;
    }
    .waffle-btn {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: calc(var(--vw) * 0.22);
      padding: calc(var(--vw) * 0.4);
      background: transparent;
      border: none;
      border-radius: calc(var(--vw) * 0.4);
      cursor: pointer;
      opacity: 0.2;
      transition: opacity 0.2s, background 0.2s;
    }
    .waffle-btn:hover { opacity: 0.6; background: rgba(0,0,0,0.04); }
    .waffle-btn.open { opacity: 0.6; background: rgba(0,0,0,0.04); }
    .waffle-dot {
      width: calc(var(--vw) * 0.32);
      height: calc(var(--vw) * 0.32);
      background: var(--color-text-inv);
      border-radius: 50%;
    }
    .waffle-dropdown {
      display: none;
      position: absolute;
      top: calc(100% + var(--vw) * 0.2);
      left: 0;
      background: #fff;
      border-radius: calc(var(--vw) * 0.6);
      box-shadow: 0 calc(var(--vw) * 0.15) calc(var(--vw) * 0.8) rgba(0,0,0,0.15);
      min-width: calc(var(--vw) * 20);
      overflow: hidden;
      font-family: 'Montserrat', sans-serif;
    }
    .waffle-dropdown.open { display: block; }
    .waffle-dropdown-header {
      padding: calc(var(--vw) * 0.5) calc(var(--vw) * 0.9);
      font-size: calc(var(--vw) * 0.65);
      font-weight: 600;
      color: #999;
      text-transform: uppercase;
      letter-spacing: 0.05em;
      border-bottom: 1px solid #eee;
    }
    .waffle-dropdown a {
      display: flex;
      align-items: center;
      gap: calc(var(--vw) * 0.5);
      padding: calc(var(--vw) * 0.6) calc(var(--vw) * 0.9);
      text-decoration: none;
      color: var(--color-text);
      font-size: calc(var(--vw) * 0.85);
      font-weight: 600;
      transition: background 0.15s;
      white-space: nowrap;
    }
    .waffle-dropdown a:hover { background: rgba(217,119,87,0.08); }
    .waffle-dropdown a.current {
      color: var(--color-accent1);
      background: rgba(217,119,87,0.06);
      pointer-events: none;
    }
    .waffle-dropdown a .viz-num {
      display: inline-flex;
      align-items: center;
      justify-content: center;
      width: calc(var(--vw) * 1.5);
      height: calc(var(--vw) * 1.5);
      border-radius: 50%;
      background: #f0ede6;
      font-size: calc(var(--vw) * 0.65);
      font-weight: 700;
      color: #999;
      flex-shrink: 0;
    }
    .waffle-dropdown a.current .viz-num {
      background: var(--color-accent1);
      color: var(--color-text-inv);
    }

    /* ── VISUALIZATION-SPECIFIC STYLES BELOW ── */

  </style>
</head>
<body>
  <div class="viewport" id="viewport">
    <div class="content-card">
      <!-- Your visualization content here.
           Layout is up to you: flexbox columns, a single diagram,
           side-by-side panels, etc. -->
    </div>

    <!-- Waffle Menu -->
    <div class="waffle-menu" id="waffleMenu">
      <button class="waffle-btn" id="waffleBtn" aria-label="Visualization menu">
        <span class="waffle-dot"></span><span class="waffle-dot"></span><span class="waffle-dot"></span>
        <span class="waffle-dot"></span><span class="waffle-dot"></span><span class="waffle-dot"></span>
        <span class="waffle-dot"></span><span class="waffle-dot"></span><span class="waffle-dot"></span>
      </button>
      <div class="waffle-dropdown" id="waffleDropdown"></div>
    </div>

    <div class="nav-bar">
      <button class="nav-btn" id="prevBtn">&#9664;</button>
      <span class="nav-indicator" id="navIndicator">1 / 1</span>
      <button class="nav-btn" id="nextBtn">&#9654;</button>
    </div>
  </div>

  <script>
    // ── Viewport sizing (strict 16:9) ──────────────────────
    const vp = document.getElementById('viewport');
    function sizeViewport() {
      const winW = window.innerWidth;
      const winH = window.innerHeight;
      const ratio = 16 / 9;
      let w, h;
      if (winW / winH > ratio) { h = winH; w = h * ratio; }
      else { w = winW; h = w / ratio; }
      vp.style.width = w + 'px';
      vp.style.height = h + 'px';
      document.documentElement.style.setProperty('--vw', (w / 100) + 'px');
    }
    window.addEventListener('resize', sizeViewport);
    sizeViewport();

    // ── Navigation ─────────────────────────────────────────
    let currentStep = 0;
    const totalSteps = 1; // Set this to your number of steps
    const prevBtn = document.getElementById('prevBtn');
    const nextBtn = document.getElementById('nextBtn');
    const navIndicator = document.getElementById('navIndicator');

    // Implement this: rebuild the scene to match the target step.
    // When animate=true (stepping forward), animate the latest addition.
    // When animate=false (stepping backward or initializing), just set state.
    function renderUpToStep(step, animate) {
      // Your logic here
    }

    function goToStep(n) {
      if (n < 0 || n >= totalSteps) return;
      const forward = n > currentStep;
      currentStep = n;
      renderUpToStep(n, forward);
      navIndicator.textContent = `${n + 1} / ${totalSteps}`;
      prevBtn.disabled = (n === 0);
      nextBtn.disabled = (n === totalSteps - 1);
    }

    document.addEventListener('keydown', (e) => {
      if (e.key === 'ArrowRight') goToStep(currentStep + 1);
      if (e.key === 'ArrowLeft') goToStep(currentStep - 1);
    });

    prevBtn.addEventListener('click', () => goToStep(currentStep - 1));
    nextBtn.addEventListener('click', () => goToStep(currentStep + 1));

    // Initialize
    navIndicator.textContent = `1 / ${totalSteps}`;
    prevBtn.disabled = true;
    nextBtn.disabled = (totalSteps <= 1);
    renderUpToStep(0, false);

    // ── Waffle Menu ─────────────────────────────────────────
    // UPDATE THIS LIST when adding new visualizations
    const VISUALIZATIONS = [
      { slug: 'context-window', title: 'The Context Window' },
      { slug: 'memory-types', title: 'LLM Memory Types' },
    ];

    const waffleBtn = document.getElementById('waffleBtn');
    const waffleDropdown = document.getElementById('waffleDropdown');
    const pathParts = window.location.pathname.replace(/\/index\.html$/, '').split('/').filter(Boolean);
    const currentSlug = pathParts[pathParts.length - 1] || '';

    const waffleHeader = document.createElement('div');
    waffleHeader.className = 'waffle-dropdown-header';
    waffleHeader.textContent = 'Visualizations';
    waffleDropdown.appendChild(waffleHeader);

    VISUALIZATIONS.forEach((viz, i) => {
      const a = document.createElement('a');
      a.href = '../' + viz.slug + '/index.html';
      const num = document.createElement('span');
      num.className = 'viz-num';
      num.textContent = i + 1;
      a.appendChild(num);
      a.appendChild(document.createTextNode(viz.title));
      if (viz.slug === currentSlug) a.classList.add('current');
      waffleDropdown.appendChild(a);
    });

    waffleBtn.addEventListener('click', (e) => {
      e.stopPropagation();
      const isOpen = waffleDropdown.classList.toggle('open');
      waffleBtn.classList.toggle('open', isOpen);
    });
    document.addEventListener('click', () => {
      waffleDropdown.classList.remove('open');
      waffleBtn.classList.remove('open');
    });

    // ── VISUALIZATION-SPECIFIC CODE BELOW ──────────────────
  </script>
</body>
</html>
```

## Adding a New Visualization

Use the `/create-visualization` skill with a description of the concept, or manually:

1. Create `visualizations/<slug>/index.html`
2. Copy the boilerplate above
3. Design your layout and implement `renderUpToStep(step, animate)`
4. Update the `VISUALIZATIONS` array in the waffle menu JS — both in the new file AND in all existing visualization files
5. Test by opening the file directly in a browser (arrow keys + nav buttons, try F11 fullscreen)
6. Add a link card to the root `index.html`
7. Commit and push — GitHub Pages auto-deploys

## Reference Implementation

See `visualizations/context-window/index.html` for a complete example showing:
- Two-column layout (chat window + bar chart) that persists across all steps
- Each step adds a chat exchange AND grows a chart bar simultaneously
- Stacked bar chart segments showing previous context vs. new tokens
- `renderUpToStep()` rebuilding any state for backward navigation
- Animations only firing when stepping forward

## Deployment

Hosted on **GitHub Pages** via the repo `morgandejongeVisma/claude-workshop-visualizations`.

- **Live URL**: https://morgandejongevisma.github.io/claude-workshop-visualizations/
- **Visualizations live at**: `https://morgandejongevisma.github.io/claude-workshop-visualizations/visualizations/<slug>/`
- **Auto-deploy**: A GitHub Actions workflow (`.github/workflows/deploy-pages.yml`) deploys on every push to `main`. No build step.
- **Local testing**: Open any HTML file directly in the browser (`file://` protocol works because all asset paths are relative)
- **To deploy**: Commit changes and `git push` — the site updates automatically within ~30 seconds.

**Important**: The `.claude/` folder and `CLAUDE.md` are excluded from the repo via `.gitignore`. Only visualization files, assets, and the workflow are deployed.

## Animation Libraries

Preferred: **GSAP 3.12** via CDN for timeline-based animations. Individual visualizations may also load D3.js or other libraries via CDN as needed. Keep all dependencies CDN-loaded — no npm, no build step.
