## Infant Star Tap Game

A minimal, touch-friendly canvas game. Stars gently twinkle and rotate across a black sky. Tap or click a star to make it pop with a soft audio chirp and fade away.

### Quick start

- Open `index.html` in any modern browser; no build step or dependencies.
- Optional: serve locally instead of opening via `file://` if you prefer a local URL.
  - Python: `python3 -m http.server 8000`
  - Node: `npx http-server -p 8000 .`

### How to play

- Stars appear periodically on the screen.
- Click (desktop) or tap (mobile) a star to pop it.
- A soft pop sound plays; the star scales down and fades out.

### Features

- Twinkling animation and gentle rotation per star
- Pop interaction with Web Audio API sine chirp
- Responsive full-screen canvas that resizes with the window
- Mouse and multi-touch support
- No external assets; stars are generated as inline SVGs

### Implementation overview

- Canvas 2D rendering (`<canvas id="gameCanvas">`)
- Each star is an object with position, size, rotation, scale, and alpha
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
// Pop sound
oscillator.frequency.setValueAtTime(600, audioCtx.currentTime);
oscillator.frequency.exponentialRampToValueAtTime(200, audioCtx.currentTime + 0.15);
gainNode.gain.setValueAtTime(0.2, audioCtx.currentTime);
gainNode.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.15);
```

### Notes on audio

- Browsers often require a user gesture to start audio. This game triggers sound on click/tap, which satisfies that requirement.
- On some mobile browsers, the first tap may initialize audio; subsequent taps will play the pop sound.

### Compatibility

- Tested in current versions of Chrome, Edge, Safari, and Firefox
- Works with mouse and touch input
- Layout is responsive to window size changes

### Folder structure

```
star-game/
├─ index.html   # The entire game
└─ README.md    # This document
```

### License

Written with gpt-5 and reviewed by me. MIT License.


