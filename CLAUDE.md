# LEDLightning Project

## Project Location
`~/Library/CloudStorage/Dropbox/05-Projects/Programming/LEDLightning/`

## What This Is
LEDLightning is a single-file, self-contained HTML application that simulates a procedural thunderstorm rendered as a glowing LED dot-matrix display. Seed-based cloud and terrain generation with dynamic lightning bolt spawning, recursive branching (Lichtenberg figures), and illumination effects on clouds and ground. Storms are deterministic — any storm can be exactly reproduced from its seed and settings. No server, no build step, no dependencies, no installation required.

---

## Repo Structure
```
LEDLightning.html   ← the application (always the current version)
README.md           ← documentation (always current, displayed on GitHub)
CLAUDE.md           ← this file
.gitignore
```

**Always read both `LEDLightning.html` and `README.md` before making any changes.**

There are no versioned files (e.g. `LEDLightning-v1_2.html`). Git history is the version history.

---

## Versioning Rules
- **Every change — including minor bug fixes — gets a new version number**
- Increment the minor version for all changes (e.g., `1.1` → `1.2`)
- Increment the major version only for significant structural rewrites
- Update the `<title>` tag in `LEDLightning.html` to reflect the new version
- Add an entry to the version history table in `README.md` describing what changed
- Version history entries should be concise (one line), e.g.:
  `| **1.2** | Added rain streaks between cloud and ground |`
- For milestone versions, create a git tag: `git tag v1.2 && git push --tags`

---

## Tech Stack Constraints
- **Vanilla HTML, CSS, and JavaScript only** — no frameworks, no libraries, no CDNs
- Everything must live in a single `.html` file — do not split into multiple files
- No npm, no build process, no transpilation
- No external dependencies of any kind
- Must work when opened directly as a local file (`file://`) with no server
- Grid: `W=100, H=100, CELL=6` → 600×600 px canvas
- Background: `#05060f`

---

## Architecture

### Scene
- **Cloud layer** (rows 4–10): two octaves of seeded 1D value noise; per-column brightness variation; gentle animation drift
- **Ground layer** (rows 88–96): two octaves of seeded 1D value noise; green gradient surface-to-depth; subtle animation drift
- **Seed** controls cloud shape, cloud brightness, terrain profile, and bolt timing sequence

### Lightning Bolts
- Bolt positions drawn from seeded PRNG — deterministic per seed
- Frequency slider adjusts average wait between bolts; 25% chance of immediate double-strike
- Recursive tracer: main channel (200 max steps, 0.995 energy decay) biased downward; branches fork probabilistically (depth up to 6, allowed to 5)
- Bolt lifecycle: flash → sustained → quick drop → slow fade (~30–54 frames total)
- Core segments (depth 0–1) get a 1px glow halo; branch segments fade by depth

### Illumination Effects
- **Cloud flash:** cells within 20 columns of spawn point flash at bolt birth (2-phase)
- **Ground flash:** cells within 15 columns of strike point illuminate when bolt hits ground

### Rendering Pipeline
Same LED dot-matrix kernel as all LED projects:
```js
const kern = new Float32Array(CELL * CELL);
const hc = CELL * 0.5;
for (let py = 0; py < CELL; py++)
  for (let px = 0; px < CELL; px++) {
    const dx = px - hc + 0.5, dy = py - hc + 0.5;
    const d = Math.sqrt(dx * dx + dy * dy) / (hc * 0.88);
    kern[py * CELL + px] = Math.max(0, 1 - d * d);
  }
// Ambient blue bleed in dark gaps: (1 - k) * 4 added to blue channel
```

### Palettes (5)
| Palette | Character |
|---|---|
| Electric Blue | Classic storm — white-blue core, blue branches |
| Purple Plasma | Alien/sci-fi — lavender core, purple branches |
| Warm Lightning | Summer heat — yellow-white core, orange branches |
| Aurora Discharge | Northern lights — mint core, green branches |
| Red Storm | Martian/volcanic — peach core, red branches |

Each palette defines: cloud base/highlight, bolt core/mid/dim, and flash colour.

### Controls
| Control | Range | Effect |
|---|---|---|
| Seed | 1–99999 | Cloud shape, terrain, bolt timing sequence |
| Frequency | 1–10 | Average bolt spawn rate |
| Branching | 1–10 | Branch probability and energy |
| Palette | dropdown | Colour theme |

### Seeded Randomness
- All randomness uses mulberry32 PRNG seeded from the seed input
- **Never use `Math.random()` in simulation or generation code** — always use the seeded RNG
- Breaking determinism is a critical bug

---

## Design Principles
1. **Determinism is sacred** — same seed + settings must always produce the identical storm
2. **Self-contained** — the file must work with no internet connection and no server
3. **No regressions** — changes must not break existing seeds or share URLs
4. **Minimal UI chrome** — the storm is the focus; controls are secondary

---

## Ideas for Future Features
- Rain streaks between cloud and ground, heavier near active bolts
- Thunder timing: visual "rumble" delayed after flash
- Multiple cloud layers for depth
- Horizon silhouettes: trees, buildings, mountains
- Storm progression: calm → building → peak → calm cycle
- Wind: horizontal drift on bolt angle bias
- URL sharing: encode seed + settings in URL hash
- Ground variety: terrain types (field, water with reflections, desert, snow)
- Unlit panel mode: show dark LED dots in gaps
- Interaction: click to trigger a bolt at a specific x position

---

## What NOT to Do
- Do not use `Math.random()` in simulation or generation code — use the seeded RNG
- Do not introduce any external dependency, CDN link, or import
- Do not split the code into multiple files
- Do not create versioned files (e.g. `LEDLightning-v1_2.html`) — edit `LEDLightning.html` directly
- Do not change the behavior of existing seeds without a clear reason

---

## Testing
Open `LEDLightning.html` directly in a browser. No build step needed.

Key things to verify after any change:
- Storm animates on load without errors
- Browser console is clean (no JS errors)
- Existing seeds still produce the same storms

---

## Git & GitHub
- Remote: https://github.com/ReeseLloyd/LEDLightning.git
- Branch: `main`
- After completing changes, commit and push:
  ```
  git add LEDLightning.html README.md
  git commit -m "v1.X: short description of what changed"
  git push
  ```
- Commit messages should describe the functional change, not the mechanism
- For milestone versions, also tag and push the tag:
  ```
  git tag v1.X
  git push --tags
  ```
