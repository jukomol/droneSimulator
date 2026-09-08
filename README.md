# 🚁 Farmland Drone Combat

A browser-based 3D drone combat simulator built with **Three.js**. Pilot an attack drone over rolling farmland, hunt down enemy UAVs flying at different altitudes, lock on, and blast them out of the sky — all in a single HTML file with zero dependencies to install.

![Three.js](https://img.shields.io/badge/Three.js-0.160-black?logo=three.js&logoColor=white)
![Type](https://img.shields.io/badge/type-Browser_Game-blue)
![License](https://img.shields.io/badge/license-MIT-green)
![No Build](https://img.shields.io/badge/build-none-required-orange)

---

## ✨ Features

### Flight Simulation
- **Physics-based flight model** — gravity, thrust, drag, angular momentum, and auto-leveling
- **6-degree-of-freedom controls** — pitch, yaw, roll, throttle, forward/lateral movement
- **Altitude hold** — toggle a PID-like controller that auto-maintains your current altitude for steady aiming
- **Collision detection** with terrain, buildings, trees, hay bales, and wind turbines
- **Crash mechanics** — hard landings destroy your drone; soft landings are fine
- **Battery system** with drain proportional to throttle and speed

### Combat System
- **Enemy UAVs** flying at different altitudes (15m–90m) with three patrol patterns: circular, figure-8, and zigzag — count and toughness scale with difficulty
- **Target lock-on** — a targeting cone highlights the nearest enemy in your crosshairs (cone width scales with difficulty)
- **Auto-aim assist** — trajectory blend toward locked targets for satisfying hits (strength scales with difficulty, down to none on Impossible)
- **Swept collision detection** — ray-segment intersection prevents projectiles from tunneling through fast-moving targets
- **Projectile pooling** — 80 pre-allocated projectiles with shared geometry/materials for zero-lag rapid fire
- **Health system** — each enemy takes multiple hits to destroy (scales with difficulty), with a floating health bar
- **Explosion particle effects** on hits and kills
- **Hit marker** flash on successful hits
- **Victory condition** — destroy every enemy UAV to complete the mission

### Difficulty Modes
Pick a difficulty on the start screen — it controls enemy count/health/speed, auto-aim assist, lock-on cone width, battery capacity/drain, and how forgiving crashes are:

| Difficulty | UAVs | Hits to Kill | Enemy Speed | Auto-Aim | Battery | Crash Tolerance |
|-----------|:---:|:---:|:---:|:---:|:---:|:---:|
| **Easy** | 6 | 2 | 0.7× | 55% | 700 | High |
| **Medium** | 10 | 3 | 1.0× | 35% | 500 | Normal |
| **Hard** | 14 | 4 | 1.4× | 18% | 380 | Low |
| **Impossible** | 18 | 5 | 1.9× | none | 260 | Very low |

The current difficulty is shown as a badge under the compass during flight, and can be changed anytime by choosing **MAIN MENU** from the crash/mission-complete screen.

### Environment
- Procedurally generated **rolling hills terrain** with color-coded crop fields (wheat, corn, soy, hay, grass, tilled)
- **5 farm complexes** — each with a barn, silo, farmhouse, and fenced pasture
- **120+ trees**, hay bales, and crop rows
- **3 animated wind turbines** with spinning blades
- **Ponds** with muddy shores
- **Roads** with lane markings running across the map
- **Gradient sky dome** with 25 drifting cloud formations
- **Dynamic sun lighting** with soft PCF shadows

### HUD & Instruments
- Speed, altitude, heading readouts
- **Compass strip** with N/E/S/W markers
- Throttle, pitch, roll, yaw, vertical speed instruments
- **Hit count, kill count, accuracy %** combat stats
- Battery indicator with color-coded warnings
- Flight timer
- **Tactical radar** showing enemy positions with altitude indicators (▲/▼), color-coded by relative height, and lock-on highlights

### Camera Modes
Toggle between three views with **V**:
1. **Chase cam** — third-person follow behind the drone
2. **FPV** — first-person view from the drone's camera
3. **Orbit** — cinematic orbit around the drone

---

## 🎮 Controls

| Key | Action |
|-----|--------|
| `W` `S` | Forward / Back |
| `A` `D` | Strafe left / right |
| `↑` `↓` | Pitch (nose down / up) |
| `←` `→` | Yaw (turn left / right) |
| `Q` `E` | Roll left / right |
| `Shift` | Ascend |
| `Ctrl` | Descend |
| `Space` | Fire weapon (hold for auto-fire) |
| `H` | Toggle altitude hold |
| `V` | Cycle camera mode (chase → FPV → orbit) |
| `R` | Full reset (drone, battery, stats, enemies) |

> **Tip:** Use `H` to lock your altitude, then use pitch (`↑`/`↓`) to aim at enemies above or below you while holding `Space`.

---

## 🚀 Quick Start

No build tools, no package manager, no server required.

### Option 1 — Direct
1. Just open `https://jukomol.github.io/droneSimulator/`
2. Read the instructions
3. Click **START COMBAT**

### Option 2 — Local server (recommended for development)
```bash
# Python
python3 -m http.server 8000

# Node
npx serve

# Then open http://localhost:8000/drone-simulator.html
```

### Requirements
- A modern browser with **WebGL** support
- An internet connection (Three.js is loaded via CDN)

---

## 🛠️ Technical Details

### Architecture
The entire game is a single self-contained HTML file (~1850 lines) with no external dependencies beyond the Three.js CDN module. All code is vanilla JavaScript using ES6 modules.

### Key Systems

| System | Description |
|--------|-------------|
| **Flight physics** | Euler-angle rotation with angular velocity damping, thrust along local-up, gravity, air drag, and auto-leveling |
| **Altitude hold** | PID-like controller: `hoverThrottle + altError * Kp + vSpeed * Kd` |
| **Projectile pool** | 80 pre-allocated `Mesh` objects with shared `SphereGeometry`/`MeshBasicMaterial`, reused via active flag — zero per-shot allocation |
| **Collision (projectiles)** | Swept sphere-vs-sphere: closest-point-on-segment to enemy center, prevents tunneling at 120 m/s |
| **Collision (drone)** | Cylinder (XZ radius) + height band check against buildings/trees |
| **Targeting** | Forward-dot-product cone test against alive enemies within 200m; cone angle narrows as difficulty increases |
| **Auto-aim** | `lerp` of shoot direction toward locked target position; blend strength scales from 55% (Easy) down to 0% (Impossible) |
| **Enemy AI** | Three parametric patrol patterns (circle, lemniscate, zigzag) with sinusoidal altitude bobbing; patrol/bob speed scales with difficulty |
| **Difficulty** | A single config object per mode drives enemy count/health/speed, auto-aim, lock-on cone, battery capacity/drain, and crash tolerance |

### Performance Optimizations
- **Object pooling** for projectiles and explosion particles — no runtime allocation/deallocation
- **Shared geometry & materials** across all pooled projectiles
- **No per-projectile lights** — removed `PointLight` that caused lag during rapid fire
- **Shadow map** limited to 2048×2048 with a 300-unit frustum
- **Fog culling** at 550 units to reduce draw distance
- **Pixel ratio capped** at 2 to prevent excessive resolution on high-DPI displays

---

## 📁 Project Structure

```
.
├── drone-simulator.html   # The entire game (HTML + CSS + JS)
└── README.md              # This file
```

That's it. One file.

---

## 🎯 Gameplay Tips

- **Start with altitude hold** (`H`) — it frees you from managing throttle so you can focus on aiming
- **Pitch to aim** — use `↑`/`↓` to angle your shots up at high-flying enemies or down at low ones
- **Watch the radar** — red dots above you are bright red, below you are lighter; use the altitude arrows to find targets
- **Get close** — auto-aim is more effective at shorter ranges
- **Conserve battery** — altitude hold uses less power than manual throttle jockeying
- **Don't crash** — hitting the ground too hard destroys your drone; the exact threshold gets stricter as difficulty increases
- **Start on Easy** if you're new to the flight model — it's far more forgiving on crashes, battery, and auto-aim

---

## 🔄 Reset Behavior

Pressing `R` (or the **RESPAWN** button) performs a **full reset** on the current difficulty:
- ✅ Drone returns to spawn position with zero velocity
- ✅ Battery refills to 100% (of the current difficulty's capacity)
- ✅ Hit count, kill count, and shots fired reset to 0
- ✅ All projectiles cleared
- ✅ All explosion particles cleared
- ✅ All enemies respawn at new random positions and altitudes
- ✅ Altitude hold turns off
- ✅ Flight timer resets

Choosing **MAIN MENU** from the crash/mission-complete screen instead returns you to the start screen so you can pick a different difficulty.

---

## 🌐 Browser Compatibility

| Browser | Status |
|--------|--------|
| Chrome | ✅ Fully supported |
| Firefox | ✅ Fully supported |
| Edge | ✅ Fully supported |
| Safari | ✅ Fully supported |
| Mobile browsers | ⚠️ Runs but no keyboard controls |

---

## 📜 License

MIT License — free to use, modify, and distribute.

---

## 🙏 Credits

- **Three.js** — [threejs.org](https://threejs.org)
- Terrain, buildings, vegetation, and drone models are all built from procedural Three.js primitives (no external 3D assets)
