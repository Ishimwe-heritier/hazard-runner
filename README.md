# PixelSurvivor

A browser-based survival game built with Vue 3, Vite, HTML Canvas, and SASS.

**Student:** [Your Name] | **Student ID:** [Your ID]

## Live Demo

- **Netlify URL:** https://[your-site].netlify.app
- **GitHub Repository:** https://github.com/[your-username]/pixelforge-survivor

## Interaction Model

I chose **keyboard arrow-key movement** (WASD also supported) because it is the most intuitive control scheme for a non-technical audience at a youth coding camp. Participants can pick up the game and play immediately without any instruction — arrow keys are universally understood for movement in games. This avoids the frustration of click-to-move or complex keyboard combinations, letting players focus on the core survival mechanic. Research into games like *Dodge the Creeps* (Godot demo) and *Diep.io* inspired this approach, as both use simple directional movement for broad accessibility.

## Challenge Encountered

One challenge was synchronizing **hazard movement updates** with the **canvas rendering loop**. Initially, hazards spawned but didn't move because their positions were only updated in the 1-second game loop. I solved this by adding a second interval (50ms) dedicated purely to updating hazard positions and running collision detection, while the `requestAnimationFrame` loop in `GameBoard.vue` handles smooth visual rendering independently. Keeping the game-logic interval at 1 second (for scoring and spawning) separate from the movement interval keeps the code maintainable and the game responsive.

## Features

- **Reactive HUD** — Score, health bar, survival time, and difficulty level update live via `ref()` and `computed()`
- **Game Loop** — `setInterval` drives score, hazard spawning, and difficulty scaling
- **Random Hazards** — Red enemy circles spawn at random edges with random velocities using `Math.random()`
- **Difficulty Scaling** — Every 10 points, spawn rate and hazard speed increase; difficulty level is displayed
- **Game Over Overlay** — Shown via `v-if` with final score, high score, and restart button
- **High Score Tracker** — Best score is tracked in Vue state during the session
- **SASS Styling** — Color variables, nested selectors, and custom mixins (`glass-panel`, `glowing-border`)

## Project Setup

```bash
npm install
npm run dev
```

### Build for Production

```bash
npm run build
npm run preview
```

## Tech Stack

- **Vue 3** — Composition API with `<script setup>`
- **Vite** — Build tool
- **HTML Canvas** — Game rendering
- **SASS** — Styling
- **Netlify** — Deployment

## Component Tree

```
App.vue
├── GameHUD.vue   — Displays score, health bar, timer, difficulty
├── GameBoard.vue — Canvas rendering, keyboard input
└── GameOver.vue  — Overlay with score and restart (v-if)
```
