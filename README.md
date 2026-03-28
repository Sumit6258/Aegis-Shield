# ⚡ AEGIS SHIELD
### Next-Generation Air Defence Interceptor Simulator

[![Live Demo](https://img.shields.io/badge/▶_LIVE_DEMO-LAUNCH_SIMULATION-00ff88?style=for-the-badge&labelColor=001408&color=00ff88)](https://sumit6258.github.io/Aegis-Shield/)
![Three.js](https://img.shields.io/badge/Three.js-r128-blue?style=flat-square)
![JavaScript](https://img.shields.io/badge/JavaScript-ES2022-yellow?style=flat-square)
![Physics](https://img.shields.io/badge/Physics-PN_Guidance-red?style=flat-square)
![AI](https://img.shields.io/badge/AI-Threat_Prioritization-purple?style=flat-square)

---

> **A production-grade, physics-accurate air defence simulation demonstrating real ballistic guidance algorithms, AI threat prioritization, and military-grade HUD design — built entirely in a single HTML file with no build tools.**

---

## 🎯 What Is This?

AEGIS SHIELD is a real-time 3D tactical air defence simulator that models how modern **Ballistic Missile Defence Systems (BMDS)** work. It simulates the entire engagement sequence:

1. **Radar Detection** — Targets enter the scan zone; detection probability is computed using a simplified Blake radar range equation
2. **Threat Classification** — An AI scores each target on proximity, speed, type, and stealth characteristics
3. **Intercept Solution** — Proportional Navigation (PN) guidance calculates the optimal engagement trajectory
4. **Kill Assessment** — Probability of Kill (Pk) is computed and displayed in real-time

This is not a simple game — it's an engineering simulation with real military guidance law mathematics.

---

## 🚀 Live Demo

| Action | Result |
|---|---|
| Click **COMPOUND ASSAULT** | Launches all threat types simultaneously |
| Toggle **AUTO DEF** | AI system engages all threats by priority |
| Click a **threat row** | Locks target and shows live physics equations |
| Press **F** | Cycles camera to follow an interceptor |
| Press **M** | Activates slow-motion cinematic mode |

---

## 🏗️ System Architecture

```
AEGIS SHIELD v4.0
├── § 1  CONSTANTS & CONFIG       — ADS systems, threat definitions
├── § 2  THREE.JS RENDERER        — WebGL, ACESFilmic tone mapping, shadows
├── § 3  CAMERA CONTROLLER        — Free orbit | Follow target | Overview
├── § 4  LIGHTING                 — Physically correct moon + fill + base lights
├── § 5  WORLD BUILDER            — Noise terrain, PBR materials, atmosphere
├── § 6  PHYSICS ENGINE           — PN guidance, ballistic arcs, drag, Pk
├── § 7  AI DEFENSE SYSTEM        — Threat scoring, priority queue, auto-engage
├── § 8  ENTITY FACTORIES         — PBR mesh construction (jets, missiles, drones)
├── § 9  TARGET MANAGEMENT        — Spawn, update, stochastic flight physics
├── § 10 INTERCEPTOR MANAGEMENT   — Launch, PN guidance, exhaust smoke, kill check
├── § 11 EXPLOSION SYSTEM         — Particle bursts, shockwave rings, debris
├── § 12 RADAR SYSTEM             — 2D scope: sweep, blips, IFF classification
├── § 13 DETECTION SYSTEM         — Radar equation probability, stealth modeling
├── § 14 KILL EFFECT              — Screen-space rings + SPLASH! UI overlay
├── § 15 AUDIO SYSTEM             — Web Audio API procedural sound synthesis
├── § 16 LOG SYSTEM               — Timestamped engagement event log
├── § 17 UI SYSTEM                — Modular HUD panels, live equations display
├── § 18 CONTROLS                 — Input handlers, keyboard shortcuts
├── § 19 GAME LOOP                — Fixed timestep, slow-motion, entity cleanup
└── § 20 BOOT SEQUENCE            — Progressive loading, auto-demo
```

---

## 🔬 Engineering Concepts

### Proportional Navigation (PN) Guidance

The most important algorithm in the system. PN is the guidance law used by real interceptor missiles including the AIM-120 AMRAAM and Patriot PAC-3.

```
a_cmd = N · V_c · λ̇ × ê_LOS
```

| Variable | Meaning |
|---|---|
| `N` | Navigation constant (3–6; higher = more aggressive) |
| `V_c` | Closing velocity — rate at which range decreases |
| `λ̇` | Line-of-sight angular rate (rad/s) |
| `ê_LOS` | Unit vector along the line of sight |

**3D Implementation:**
```javascript
// LOS vector and range
const r_vec = tgt.pos.clone().sub(inter.pos);
const r     = r_vec.length();

// Relative velocity
const v_rel = tgt.vel.clone().sub(inter.vel);

// Closing velocity (positive when closing)
const V_c   = -r_vec.dot(v_rel) / r;

// LOS rate: λ̇ = (r × v_rel) / |r|²
const los_rate = r_vec.clone().cross(v_rel).divideScalar(r * r);

// Commanded acceleration
const a_cmd = los_rate.clone().cross(r_vec.normalize())
                              .multiplyScalar(N * Math.max(V_c, 0));
```

### Ballistic Trajectory

```
y(t) = y_peak · 4 · frac · (1 - frac)
```

Missiles follow a parabolic arc with terminal acceleration (gravity-driven dive). Speed increases in the terminal phase modelling real physics:
```
v_terminal = v_base · (1 + (frac - 0.55) · 2.2)
```

### Radar Detection Probability (Blake Equation)

```
P_detect = σ · (R_max / R)⁴ · P_noise
```

| Variable | Meaning |
|---|---|
| `σ` | Radar Cross-Section (m²) |
| `R_max` | Maximum radar range |
| `R` | Actual range to target |
| `P_noise` | Random noise component [0.7, 1.0] |

This means stealth aircraft (`σ = 0.05 m²`) are significantly harder to detect than conventional jets (`σ = 0.60 m²`).

### Probability of Kill (Pk)

```
Pk = P_guidance · P_fuze · P_lethality
```

Calculated from:
- Speed ratio between interceptor and target
- Intercept range vs maximum engagement range
- Target maneuverability class
- Target RCS (stealth penalty)
- ADS kill radius

### AI Threat Priority Score

```
Score = 40·dist_score + 30·speed_score + 3·type_weight + 15·alt_score + 10·rcs_score
```

The AI sorts all detected threats by score and engages highest-priority targets first, avoiding wasting interceptors on easy low-priority drones when a hypersonic missile is inbound.

### Aerodynamic Drag

```
F_drag ≈ ½ · ρ · Cd · A · v²
```

Simplified as: `deceleration ∝ drag_coeff · v²`

Each threat type has a drag coefficient — hypersonic missiles have very low drag (0.002), drones have higher drag (0.012).

---

## 🎮 Air Defence Systems

| System | Speed | Kill Radius | Range | Nav N | Role |
|---|---|---|---|---|---|
| Iron Dome | 14 km/s | 7 km | 90 km | 4 | Short-range, drones/rockets |
| Patriot PAC-3 | 16 km/s | 7 km | 90 km | 4 | Medium-range, cruise/ballistic |
| S-400 Triumph | 18 km/s | 9 km | 130 km | 5 | Long-range, area denial |
| THAAD | 22 km/s | 11 km | 210 km | 5 | Theatre ballistic missile defence |
| Arrow-3 | 26 km/s | 13 km | 260 km | 6 | Exoatmospheric, ICBM intercept |

---

## 🎯 Threat Library

| Platform | Type | Speed | Altitude | RCS | Stealth Level |
|---|---|---|---|---|---|
| Shahed-136 | Loitering Drone | 0.8 km/s | 400m | 0.35 m² | Low |
| Tomahawk LACM | Cruise Missile | 2.5 km/s | 80m | 0.25 m² | Medium |
| Iskander SRBM | Ballistic Missile | 6.0 km/s | 55km arc | 0.20 m² | Medium |
| Su-57 Felon | Fighter Jet | 3.2 km/s | 8km | 0.60 m² | Low (evades) |
| B-21 Raider | Stealth Bomber | 2.8 km/s | 12km | 0.05 m² | **Extreme** |
| Kinzhal | Hypersonic | 10 km/s | 20km arc | 0.15 m² | High |

---

## ✨ Visual Systems

### 3D Rendering
- **Three.js r128** with WebGL 2.0
- **PBR Materials** (`MeshStandardMaterial`) for all objects — roughness, metalness, emissive
- **ACES Filmic** tone mapping for cinema-quality colour grading
- **PCF Soft Shadow Maps** on terrain
- **Instanced/pooled geometry** for particle systems

### Terrain
- **Noise-based heightmap** using summed sine waves (fractal approximation)
- **Vertex colour blending** — dirt/grass/rock transitions by elevation
- **Base zone flattening** — terrain smoothly flattens around the command base

### Atmosphere
- **Exponential fog** (`FogExp2`) matches real atmospheric scattering
- **Layered haze cylinders** at altitude — simulates atmospheric depth
- **4000-star field** with colour-varied star points
- **Perimeter lighting** — 12 military-style ground lamps

### Effects
- **3-ring shockwave system** — primary, secondary, and fireball rings expanding after each kill
- **Particle debris system** — 140 particles with gravity for major kills
- **Exhaust smoke** — per-particle velocity + colour interpolation
- **Engine glow** — pulsing `MeshBasicMaterial` sphere on every missile
- **Screen-space kill rings** — projected from world to screen coordinates

---

## 📡 Radar Scope

The 2D radar canvas implements a genuine-looking military PPI (Plan Position Indicator) scope:

- **Phosphor persistence** — sweep trail fades naturally over 40 frames
- **IFF classification** — different symbols per threat type (cross = ballistic, diamond = aircraft, dot = drone/cruise)
- **Selection ring** — double-ring with dashed outer circle for locked target
- **Blake SNR scale** — blip size and brightness correlate with target RCS
- **Compass bearing** markers at N/E/S/W

---

## 🔊 Audio System

All audio is procedurally synthesised using the **Web Audio API** — no external files required:

| Event | Synthesis Method |
|---|---|
| Radar sweep | Oscillator with exponential frequency ramp |
| Missile launch | Sawtooth wave + noise buffer (exhaust roar) |
| Explosion | White noise burst + sub-bass oscillator |
| Alert | Square wave multi-tone siren |
| Critical warning | Three-pulse square wave at 880Hz |

---

## 🛠 Tech Stack

```
Runtime        Three.js r128       3D rendering, scene graph, materials
Rendering      WebGL 2.0           Hardware-accelerated GPU rendering  
Physics        Custom (vanilla JS) PN guidance, ballistics, drag, Pk
Audio          Web Audio API       Procedural synthesis, no files
Fonts          Google Fonts        Orbitron, Share Tech Mono, Rajdhani
Deployment     GitHub Pages        Zero-config, single HTML file
```

No build tools. No webpack. No npm. Pure browser-native.

---

## 📁 Single-File Architecture

The entire simulation runs from `index.html`. The JavaScript is organized into **20 numbered sections** with JSDoc comments explaining each algorithm:

```
§ 1  Constants & Config       ~50 lines
§ 2  Renderer + Scene         ~25 lines
§ 3  Camera Controller        ~55 lines    ← 3 camera modes
§ 4  Lighting                 ~30 lines    ← PBR lighting rig
§ 5  World Builder            ~130 lines   ← Noise terrain + base
§ 6  Physics Engine           ~110 lines   ← PN, ballistic, drag, Pk
§ 7  AI Defense System        ~75 lines    ← Threat scoring + queue
§ 8  Entity Factories         ~100 lines   ← PBR mesh builders
§ 9  Target Management        ~90 lines
§ 10 Interceptor Management   ~130 lines
§ 11 Explosion System         ~95 lines    ← Shockwave rings
§ 12 Radar System             ~95 lines    ← IFF scope
§ 13 Detection System         ~35 lines    ← Blake equation
§ 14 Kill Effect              ~20 lines    ← Screen-space
§ 15 Audio System             ~75 lines    ← Web Audio synthesis
§ 16 Log System               ~12 lines
§ 17 UI System                ~120 lines   ← Live equations
§ 18 Controls                 ~80 lines
§ 19 Main Loop                ~70 lines
§ 20 Boot Sequence            ~50 lines
```

---

## 🌟 Why This Project Stands Out

| Engineering Challenge | Implementation |
|---|---|
| **Real guidance algorithm** | Proportional Navigation, not "fly toward target" |
| **Detection modeling** | Radar equation, not boolean in-range check |
| **AI with priority queue** | Score-based targeting, not first-come-first-served |
| **Ballistic phase modeling** | Boost → mid-course → terminal with gravity |
| **Live physics display** | Equations update in real-time as interceptor closes |
| **Zero dependencies** | Only Three.js from CDN — no framework, no build step |
| **Single-file delivery** | Works on GitHub Pages with one drag-and-drop |
| **60fps at high entity count** | Trail geometry pooling, UI update batching (every 12 frames) |

---

## 🔭 Future Scope

- [ ] **ECEF coordinate system** — Real Earth-surface geometry with WGS84
- [ ] **Real radar cross-section data** — Import from public RCS databases
- [ ] **Multiplayer** — WebRTC or WebSocket for cooperative defence scenarios
- [ ] **GLTF models** — Replace procedural meshes with real aircraft models
- [ ] **Postprocessing bloom** — UnrealBloomPass for missile exhaust glow
- [ ] **Terrain heightmap** — Import real DEM elevation data
- [ ] **Salvo doctrine** — Multiple interceptor assignment per high-Pk threat
- [ ] **Jamming simulation** — ECM degrading radar detection probability
- [ ] **Mobile touch controls** — Pinch zoom, tap-to-engage

---


---

## 📜 License

MIT — use freely, attribution appreciated.

---

*Built as a portfolio engineering project demonstrating simulation systems, guidance algorithms, and real-time 3D graphics.*
