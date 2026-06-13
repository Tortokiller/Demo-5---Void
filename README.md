# Void — Generative Particle Field

> Interactive generative art piece built to flex CSS and JavaScript fundamentals.  
> 280 lines. Zero dependencies. One file.

![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=flat&logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=flat&logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/ES6+-F7DF1E?style=flat&logo=javascript&logoColor=black)

---

## What This Is

Void is a real-time particle system rendered on Canvas 2D with a CSS-driven glassmorphic HUD overlay. The entire thing is a single HTML file — no build tools, no libraries, no images. It exists to demonstrate senior-level command of CSS and vanilla JavaScript in a compact, readable codebase.

Move your cursor to influence the field. Click to burst particles. Switch between three physics modes. Watch the hue cycle itself through CSS `@property`.

---

## CSS Techniques Demonstrated

| Technique                          | Where / How                                                              |
|------------------------------------|--------------------------------------------------------------------------|
| **`@property`**                    | `--hue`, `--glow-x`, `--glow-y` — registered custom properties with defined syntax, enabling CSS-native animation of values that are normally not interpolatable |
| **CSS-animated custom properties** | `@keyframes hue-drift` transitions `--hue` from 220→580, consumed by `hsl()` across multiple elements simultaneously — single source of truth for the entire color scheme |
| **`backdrop-filter` glassmorphism**| HUD panels use `blur(12px) saturate(1.4)` over a live canvas — real frosted glass, not a background trick |
| **`mix-blend-mode: screen`**       | Canvas composites additively against the dark background, making particle overlaps glow naturally |
| **CSS Grid named areas**           | HUD layout via `grid-template` with named areas (`tl`, `tr`, `bl`, `br`) — four-corner layout in 4 lines of CSS |
| **`clamp()` fluid everything**     | Font sizes, padding — all fluid without a single `@media` query. Responsive by math, not breakpoints |
| **Responsive without media queries** | `.hide-mobile` uses `opacity: clamp(0, (100vw - 500px) * 999, 1)` — a binary visibility toggle derived purely from viewport width |
| **Pure CSS scanlines**             | `::after` pseudo-element with `repeating-linear-gradient` at 4px intervals — CRT texture with zero assets |
| **Pure CSS ambient glow**          | `radial-gradient` with CSS-variable position, transitioned via `@property` — tracks cursor with eased delay |
| **Custom property cascade**        | `--hue` defined on `body`, consumed by `.title em`, `.mono span`, `.btn.active`, `.glow`, particle `hsl()` — one animation drives everything |

---

## JavaScript Techniques Demonstrated

| Technique                         | Where / How                                                               |
|-----------------------------------|---------------------------------------------------------------------------|
| **ES6 class with encapsulation**  | `Particle` class — constructor, methods, getter (`get dead`), clean state per instance |
| **Canvas 2D particle system**     | Manual render loop: spawn → update physics → draw → garbage collect dead particles per frame |
| **`requestAnimationFrame` loop**  | Proper rAF pattern with timestamp delta for FPS calculation               |
| **Frame-accurate FPS counter**    | Rolling 500ms accumulator with frame count — no `setInterval` jitter      |
| **Three physics modes**           | `orbit` (tangential force), `swarm` (attraction), `explode` (repulsion) — same force math, different vector application |
| **`Math.hypot` distance**         | Clean distance calculation without manual sqrt, used for force falloff    |
| **Object pool pattern**           | Hard cap at 600 particles, `splice` removal of dead particles in reverse iteration to avoid index shifting |
| **Edge wrapping**                 | Particles re-enter from opposite edges — toroidal topology without boundary checks per-axis |
| **Trail rendering**               | Semi-transparent rect overlay (`rgba(5,5,10,.12)`) instead of `clearRect` — previous frames persist as motion blur |
| **CSS↔JS bridge**                 | `getComputedStyle` reads the CSS-animated `--hue` value each frame, feeding it into Canvas `hsl()` colors — CSS controls the palette, JS consumes it |
| **Pointer events**                | `pointermove` for cursor tracking + glow position, `pointerdown` for burst spawn — unified touch/mouse handling |
| **Event delegation on buttons**   | `querySelectorAll('[data-mode]')` with `dataset.mode` — no IDs, no inline handlers, data-attribute driven |
| **DPR-aware canvas**              | `devicePixelRatio` scaling on resize for crisp rendering on Retina displays |

---

## Architecture

```
void/
├── index.html    # ~280 lines — markup, styles, scripts
└── README.md
```

### Render Pipeline

```
rAF callback
  ├── FPS accumulator update
  ├── Read CSS --hue via getComputedStyle
  ├── Draw trail overlay (semi-transparent fillRect)
  ├── Auto-spawn 2 particles every 3rd frame (if under cap)
  ├── Reverse iterate particles[]
  │     ├── .update() — apply forces based on mode, dampen, wrap edges
  │     ├── .draw()   — arc + hsl fill with life-based alpha
  │     └── if .dead  — splice from array
  └── Update HUD counters
```

---

## Performance Profile

| Metric              | Value                        |
|---------------------|------------------------------|
| File size           | ~8KB (gzipped)               |
| Particle cap        | 600                          |
| Target framerate    | 60fps                        |
| External requests   | 0                            |
| DOM nodes           | 18                           |
| JS libraries        | 0                            |
| Images              | 0                            |

The heaviest operation is the Canvas draw loop. With 600 particles, a mid-range laptop holds 60fps. The CSS layer (glow, scanlines, glassmorphism) runs entirely on the compositor — zero layout thrashing.

---

## Controls

| Input                | Action                              |
|----------------------|-------------------------------------|
| **Move cursor**      | Influence particle field + glow     |
| **Click**            | Burst 30 particles at pointer       |
| **Orbit button**     | Particles orbit around cursor       |
| **Swarm button**     | Particles attract toward cursor     |
| **Explode button**   | Particles repel from cursor         |

---

## Local Development

```bash
open index.html
```

That's it. No install, no build, no server required.

---

## License

Available for personal and commercial use.

---

<div align="center">

⚡ **Built by [GetWeb.tech](https://getweb.tech)** · Professional web design for small businesses · Barcelona · 2026

</div>
