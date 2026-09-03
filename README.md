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
- **10 enemy UAVs** flying at different altitudes (15m–90m) with three patrol patterns: circular, figure-8, and zigzag
- **Target lock-on** — a targeting cone highlights the nearest enemy in your crosshairs
- **Auto-aim assist** — 35% trajectory blend toward locked targets for satisfying hits
- **Swept collision detection** — ray-segment intersection prevents projectiles from tunneling through fast-moving targets
- **Projectile pooling** — 80 pre-allocated projectiles with shared geometry/materials for zero-lag rapid fire
- **Health system** — each enemy takes 3 hits to destroy, with a floating health bar
- **Explosion particle effects** on hits and kills
- **Hit marker** flash on successful hits
- **Victory condition** — destroy all 10 enemies to complete the mission

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
| **Targeting** | Forward-dot-product cone test (36°) against alive enemies within 200m |
| **Auto-aim** | 35% `lerp` of shoot direction toward locked target position |
| **Enemy AI** | Three parametric patrol patterns (circle, lemniscate, zigzag) with sinusoidal altitude bobbing |

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
- **Don't crash** — hitting the ground above 12 m/s vertical speed or 25 m/s total speed destroys your drone

---

## 🔄 Reset Behavior

Pressing `R` (or the reset button) performs a **full reset**:
- ✅ Drone returns to spawn position with zero velocity
- ✅ Battery refills to 100%
- ✅ Hit count, kill count, and shots fired reset to 0
- ✅ All projectiles cleared
- ✅ All explosion particles cleared
- ✅ All 10 enemies respawn at new random positions and altitudes
- ✅ Altitude hold turns off
- ✅ Flight timer resets

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
