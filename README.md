## Star Game

A touch-friendly canvas game. Stars twinkle and rotate across a black sky. Tap or click stars to pop them with a soft audio chirp, earn points based on color, and watch them fade away. Choose a difficulty, toggle sound, and optionally play in full screen.

### Quick start

- Open `index.html` in any modern browser; no build step or dependencies.
- Optional: serve locally instead of opening via `file://` if you prefer a local URL.
  - Python: `python3 -m http.server 8000`
  - Node: `npx http-server -p 8000 .`

### How to play

- On the start screen, choose a difficulty (Easy, Medium, Hard) and toggle sound. You can enter Full Screen for an immersive view.
- Stars appear periodically on the screen.
- Click (desktop) or tap (mobile) a star to pop it and add its points to your score. Color determines points; see the legend.
- Medium/Hard: stars drift and bounce off edges. Hard: stars also expire after ~3–4.5 seconds, fading as time runs out.
- Use Pause to bring the start screen back, adjust options, and resume.

### Features

- Start screen with options: Difficulty (Easy/Medium/Hard), Sound toggle, Full Screen
- Points system with HUD (top-right) and animated +score popups
- Points legend showing 16 color tiers (from 5 up to 200 points)
- Twinkling animation and gentle rotation per star
- Medium/Hard: moving/bouncing stars; Hard: timed lifetime per star
- Pop interaction with Web Audio API sine chirp
- Responsive full-screen canvas that resizes with the window
- Mouse and multi-touch support
- No external assets; stars are generated as inline SVGs

### Implementation overview

- Canvas 2D rendering (`<canvas id="gameCanvas">`)
- Each star is an object with position, size, rotation, scale, alpha, points, optional velocity (Medium/Hard), and optional lifetime (Hard)
- Stars drawn from an on-the-fly SVG (data URL), so there are no image files
- Game loop uses `requestAnimationFrame` to update and render

### Configuration (tweak in `index.html`)

You can adjust gameplay feel by editing a few values in `index.html`:

- Spawn interval and cap:
  - Maximum concurrent stars: change `if (stars.length < 10)`
  - Spawn period (ms): change `setInterval(..., 1000)`
- Star size range:
  - Change `const size = 72 + Math.random() * 120` to tune min/max size
- Twinkle speed and amplitude:
  - In `update()`, `this.twinklePhase += 0.05` (speed)
  - `this.scale = 1 + Math.sin(this.twinklePhase) * 0.05` (amplitude)
- Rotation:
  - Initial random rotation set in the star constructor; add per-frame rotation if desired
- Movement speed (Medium/Hard):
  - Medium: ~`0.5 – 1.3 px/frame`; Hard: ~`1.2 – 2.4 px/frame`
- Lifetime (Hard):
  - `3000 – 4500 ms` per star; stars fade as time runs out
- Points and colors (Medium/Hard):
  - Edit the `colorTiers` array to change colors and point values
- Pop sound (Web Audio API):
  - Initial frequency: `600 Hz`, target frequency: `200 Hz`
  - Duration: ~`0.15 s`
  - Volume (gain): `0.2` (soft). Edit in `playPopSound()`

Example snippets you can look for in `index.html`:

```js
// Spawn settings
setInterval(() => {
  if (stars.length < 10) {
    spawnStar();
  }
}, 1000);
```

```js
// Star size
const size = 72 + Math.random() * 120;
```

```js
// Twinkle animation
this.twinklePhase += 0.05;
this.scale = 1 + Math.sin(this.twinklePhase) * 0.05;
```

```js
// Difficulty-based movement
if (difficulty === "medium" || difficulty === "hard") {
  const angle = Math.random() * Math.PI * 2;
  const speed = (difficulty === "hard")
    ? (1.2 + Math.random() * 1.2)
    : (0.5 + Math.random() * 0.8);
  this.vx = Math.cos(angle) * speed;
  this.vy = Math.sin(angle) * speed;
}
```

```js
// Hard-mode lifetime
if (difficulty === "hard") {
  this.createdAtMs = performance.now();
  this.lifetimeMs = 3000 + Math.random() * 1500;
}
```

```js
// Points by color tier (edit to change scoring/colors)
const colorTiers = [
  { color: "#ffffff", points: 5 },
  // ...
  { color: "#ffd700", points: 200 },
];
```

```js
// Pop sound
oscillator.frequency.setValueAtTime(600, audioCtx.currentTime);
oscillator.frequency.exponentialRampToValueAtTime(200, audioCtx.currentTime + 0.15);
gainNode.gain.setValueAtTime(0.2, audioCtx.currentTime);
gainNode.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.15);
```

```js
// Scoring on pop (+score popup and HUD)
const delta = stars[i].points || 0;
score += delta;
scoreHud.textContent = String(score);
showScorePopup(delta);
```

### Notes on audio

- Browsers often require a user gesture to start audio. This game triggers sound on click/tap, which satisfies that requirement.
- On some mobile browsers, the first tap may initialize audio; subsequent taps will play the pop sound.

### Accessibility

- Start screen controls are labeled and grouped with ARIA roles; all interactions are available via mouse or touch.

### Compatibility

- Tested in current versions of Chrome, Edge, Safari, and Firefox
- Works with mouse and touch input
- Layout is responsive to window size changes

### Folder structure

```
star-game/
├─ index.html   # The entire game
├─ README.md    # This document
└─ LICENSE      # MIT license text
```

### License

MIT License. See `LICENSE`.


