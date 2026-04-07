# LEDLightning — Documentation
**Version 1.3**

LEDLightning is a single-file, self-contained HTML application that simulates a procedural thunderstorm rendered as a glowing LED dot-matrix display. Storms are deterministic — any storm can be exactly reproduced from its seed and settings. No server, no build step, no dependencies, no installation required.

---

## Getting Started

Open `LEDLightning.html` in any modern web browser. A storm begins animating automatically on load.

---

## Controls

| Control | Range | Effect |
|---|---|---|
| **Seed** | 1–2,147,483,647 | Determines cloud shape, terrain profile, and bolt timing sequence. The same seed always produces the same storm. |
| **Random** | button | Picks a random seed and restarts. |
| **Frequency** | 1–10 | Average bolt spawn rate. Higher = more frequent strikes. |
| **Branching** | 1–10 | Branch probability and branch energy. Higher = denser Lichtenberg figures. |
| **Palette** | dropdown | Colour theme for clouds, bolts, and flash effects. |

---

## How It Works

### Scene
The storm scene is generated from the seed:
- **Cloud layer** (top of screen): two octaves of 1D value noise define the cloud bottom contour and per-column brightness. Clouds gently animate — shape drifts slowly, brightness shimmers.
- **Ground layer** (bottom of screen): two octaves of 1D value noise define the terrain surface. Green gradient from bright surface tips to darker depth. Subtle animation drift.

### Lightning Bolts
Bolts spawn from random positions along the cloud bottom. Timing is seeded and deterministic. Each bolt is traced recursively:
- **Main channel**: up to 200 steps, energy decays slowly (0.995/step), angle biased downward with random jaggedness. Terminates on ground contact.
- **Branches**: fork from the main channel or from parent branches. Probability controlled by the Branching slider. Up to 6 levels of recursion, each level inheriting 50–80% of parent energy.

Each bolt follows a lifecycle: brief full-brightness flash → sustained glow with flicker → quick fade → slow decay. Total life: ~30–54 frames.

### Illumination Effects
- **Cloud flash**: when a bolt spawns, clouds within 20 columns of the spawn point flash with the palette's flash colour.
- **Ground flash**: when a bolt hits the ground, the strike area illuminates, blending the flash colour into the ground green.

---

## Palettes

| Palette | Character |
|---|---|
| **Electric Blue** | Classic storm — white-blue core, blue branches, cool grey clouds |
| **Purple Plasma** | Alien/sci-fi — lavender core, purple branches, violet clouds |
| **Warm Lightning** | Summer heat — yellow-white core, orange branches, amber clouds |
| **Aurora Discharge** | Northern lights — mint core, green branches, teal clouds |
| **Red Storm** | Martian/volcanic — peach core, red branches, russet clouds |
| **Mixed** | Each bolt independently picks a random colour from the five palettes above |

---

## Version History

| Version | Description |
|---|---|
| **1.3** | Static ground geometry, silhouette objects (pine, round tree, house, barn, rock, bush, lamp post), strike afterglow fades over ~2 s |
| **1.2** | Variable bolt width — each bolt gets a random thickness (thin to wide), branches taper by depth |
| **1.1** | Sidebar layout, CSS-scaled view pane, random seed on startup, seed clamped to 32-bit max, Mixed palette |
| **1.0** | Initial release — cloud/terrain generation, recursive lightning, 5 palettes, illumination effects |
