# Zach Lucas — Photography Portfolio

A photography portfolio I built for my friend Zach Lucas. Wanted to push my UI/frontend design skills and create something that actually feels like a gallery, not a template.

## Stack

- **Svelte** + **Vite**
- Bodoni Moda / Hanken Grotesk / IBM Plex Mono
- Pure CSS animations (no libraries)

## Run it

```bash
cd portfolio-svelte
npm install
npm run dev
```

Opens at `http://localhost:5173`

## What's in it

- Fullscreen one-at-a-time photo viewer with darkroom "developing" transition (desaturated → full color on each photo load)
- Film strip contact sheet at the bottom for jumping between all 20 photos
- Letter-by-letter blur reveal on the hero
- About overlay written in first person from Zach's perspective
- Exhibition-style info card below each photo with expandable artist notes
- Film grain overlay, ambient safelight glow, corner registration marks
- Keyboard nav (arrows, space, escape)
- Responsive down to mobile

## Customize

Photos go in `portfolio-svelte/public/photos/`. Metadata lives in `src/lib/photoData.js`.

Colors are CSS variables in `src/app.css`:

```css
:root {
  --bg: #0e1f10;
  --paper: #e8e0d0;
  --amber: #c4956a;
}
```

## Build

```bash
npm run build
```

~20KB gzipped JS, ~4KB CSS.

---

Built with assistance from Claude.
