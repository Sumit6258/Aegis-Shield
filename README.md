# ⚡ AEGIS SHIELD
### Integrated Air Defence Warfare Simulator - v5.0

[![Live Demo](https://img.shields.io/badge/▶_LIVE_DEMO-LAUNCH_SIMULATION-00ff88?style=for-the-badge&labelColor=001408&color=00ff88)](https://sumit6258.github.io/Aegis-Shield/)
![Three.js](https://img.shields.io/badge/Three.js-r128-blue?style=flat-square)
![JavaScript](https://img.shields.io/badge/JavaScript-ES2022-yellow?style=flat-square)
![Physics](https://img.shields.io/badge/Physics-APN_Guidance-red?style=flat-square)
![AI](https://img.shields.io/badge/AI-Multi--Layer_IADS-purple?style=flat-square)
![EW](https://img.shields.io/badge/EW-Jamming_%2B_ECM-orange?style=flat-square)
![India](https://img.shields.io/badge/🇮🇳-Indigenous_Systems-green?style=flat-square)

---

> **A production-grade, physics-accurate Integrated Air Defence System (IADS) simulator featuring multi-layer defence, electronic warfare, MIRV warheads, Laser DEW, CIWS, and Indian indigenous systems including BrahMos - built entirely in a single HTML file with no build tools.**

---

## 🎯 What Is This?

AEGIS SHIELD v5 is a real-time 3D tactical warfare simulator modelling a complete **Integrated Air Defence System (IADS)** - the same layered architecture used by NATO, DRDO, and modern militaries. It simulates the full kill chain:

1. **Radar Detection** - Blake range equation with ECM/jamming degradation and ghost-blip generation
2. **Electronic Warfare** - Jamming degrades detection; ARM missiles knock radar offline for 20 seconds
3. **AI Threat Prioritization** - Multi-factor scoring assigns best available interceptor per threat
4. **Multi-Layer Engagement** - L1 exo-atmospheric → L2 medium → L3 short → L4 point defence (CIWS/Laser)
5. **Intercept Solution** - Augmented Lead Pursuit guidance with terminal phase logging
6. **Kill Assessment** - Pk calculated from speed ratio, range, RCS, ECM state, and ADS kill radius

This is not a game - it's a defence engineering simulation with real military doctrine.

---

## 🚀 Live Demo

**[▶ Launch Simulation](https://sumit6258.github.io/Aegis-Shield/)**

| Action | Result |
|---|---|
| Click **GULF WAR SIM** | Mixed ballistic, cruise, jets, F-35s |
| Click **EW BLACKOUT** | ARM missiles kill radar → stealth jets attack blind |
| Click **MIRV STRIKE** | Bus missile splits into 3–5 warheads at apogee |
| Toggle **MULTI-LAYER** `[M]` | All 13 systems engage simultaneously by range |
| Click **BRAHMOS STRIKE** 🇮🇳 | Mach 3.5 sea-skimmers with terminal pop-up dive |
| Toggle **AUTO DEF** `[A]` | AI system assigns optimal interceptor per threat |
| Press `[F]` | Cycle camera: Free Orbit → Follow Interceptor → Overview |
| Press `[X]` | Slow-motion cinematic mode |
| Press `[9]` | BrahMos Supersonic Strike scenario |
| Press `[0]` | India-Pakistan Conflict simulation |

---

## 🏗️ System Architecture

```
AEGIS SHIELD v5.0 - Integrated Air Defence Warfare Simulator
├── § 1   DEFENCE SYSTEM DEFINITIONS  - 13 ADS, 17 threat types w/ real specs
├── § 2   THREAT DEFINITIONS          - RCS, speed, altitude, drag, ECM, typeW
├── § 3   SIMULATION STATE            - SIM object, EW state, jamming, laser energy
├── § 4   THREE.JS RENDERER           - WebGL, ACESFilmic, PCF shadows, fog
├── § 5   CAMERA CONTROLLER           - Free orbit | Follow | Overview (3 modes)
├── § 6   LIGHTING + WORLD            - PBR terrain, vertex colour, stars, base
├── § 7   GUIDANCE ENGINE             - Augmented Lead Pursuit (APN), tgo display
├── § 8   PHYSICS HELPERS             - Pk, detection probability, threat scoring
├── § 9   AI MULTI-LAYER ASSIGNMENT   - getBestADS(), layer routing by range
├── § 10  MESH FACTORIES              - PBR MeshStandardMaterial for all entities
├── § 11  TARGET SPAWNING + UPDATE    - 17 flight models incl. MIRV, HGV, BrahMos
├── § 12  INTERCEPTOR SYSTEM          - Launch, guidance, smoke, kill, CIWS, Laser
├── § 13  LASER DEW                   - Beam render, energy pool, screen flash
├── § 14  EXPLOSIONS                  - 3-ring shockwave, debris particles, flash
├── § 15  RADAR SCOPE (2D Canvas)     - PPI scope, IFF symbols, ghost blips, ECM
├── § 16  DETECTION SYSTEM            - Blake equation + jamming + ARM damage
├── § 17  KILL EFFECTS                - Screen-space ring projection + SPLASH
├── § 18  AUDIO                       - Web Audio API procedural synthesis
├── § 19  LOG SYSTEM                  - Timestamped military vocabulary
├── § 20  UI UPDATE                   - Flex-column layout, Pk bar, EW status
├── § 21  CONTROLS                    - All inputs, keyboard shortcuts [0-9]
├── § 22  MAIN LOOP                   - Fixed timestep, radar repair, laser recharge
└── § 23  BOOT SEQUENCE               - Progressive loading, auto Gulf War demo
```

---

## 🔬 Engineering Concepts

### Augmented Lead Pursuit Guidance (APN)

Replaces v4's Proportional Navigation which failed on geometry edge cases. The interceptor calculates where the target **will be** at intercept time and steers toward that predicted point every frame.

```
aimPoint = target.pos + target.vel × lead_time
desired  = normalize(aimPoint − interceptor.pos) × iSpeed
delta    = desired − interceptor.vel
clamped  = min(|delta|, iSpeed × N × 1.8 × dt)
```

| Variable | Meaning |
|---|---|
| `lead_time` | `min(tgo × 0.72, 15s)` - time to predicted intercept |
| `tgo` | Time-to-go: `dist / max(abs(Vc) + 0.5, iSpeed × 0.3)` |
| `N` | Navigation constant (4–6 per ADS system) |
| `Vc` | Closing speed: `vRel · rHat` |

### Ballistic / HGV Trajectory

```
y(t) = peak_alt × 4 × frac × (1 − frac)   [parabolic arc]

v_terminal = v_base × (1 + (frac − 0.55) × 2.2)   [gravity-accelerated dive]
```

**Avangard HGV** additionally applies random lateral maneuvers every 1–2.5 seconds, making intercept probability drop significantly.

### Radar Detection Probability (Blake Equation)

```
P_detect = σ_effective × (R_max / R)⁴ × noise × jamming_factor
```

| Variable | Meaning |
|---|---|
| `σ_effective` | RCS × 0.1 if ECM active, else full RCS |
| `jamming_factor` | `1 − jammingIntensity × 0.7` when EW active |
| `noise` | Random `[0.65, 1.0]` per sweep pass |

F-35 (`σ = 0.001 m²`) is **600× harder to detect** than Su-57 (`σ = 0.60 m²`).

### MIRV Separation

At 48% of ballistic arc (near apogee), the MIRV bus destructs and spawns 3–5 independent `ballistic` reentry vehicles at the bus position. Each RV has an independent trajectory toward base.

```javascript
if (frac >= 0.48 && !t.isMIRVed) {
  t.isMIRVed = true;
  const rvCount = 3 + Math.floor(Math.random() * 2);
  for (let i = 0; i < rvCount; i++) spawnTarget('ballistic', ...);
  removeTarget(t); // bus self-destructs
}
```

### Probability of Kill (Pk)

```
Pk = f(speed_ratio, range_factor, rcs_penalty, ecm_penalty, kill_radius_bonus)
```

```javascript
let pk = min(0.96, (iSpeed/tSpeed − 0.5) × 0.55 + 0.38);
pk *= (0.45 + rangeFactor × 0.55);     // worse at max range
if (tSpeed > 7)   pk *= 0.80;           // hypersonic penalty
if (rcs < 0.01)   pk *= 0.50;           // stealth penalty
if (ecmActive)    pk *= 0.65;           // ECM penalty
pk *= min(1.2, killRadius / 6);         // kill radius bonus
```

### AI Threat Priority Score

```
Score = 40·dist_score + 30·speed_score + 3·type_weight + 15·alt_score + 10·rcs_score
```

Multi-layer mode routes each threat to the optimal ADS by range:
- `dist > 150km` → Arrow-3 / PAD
- `dist > 100km` → THAAD / AAD / Barak-8
- `dist > 80km`  → S-400 / Akash-NG
- `dist > 60km`  → Patriot PAC-3
- `dist > 20km`  → Iron Dome / NASAMS / QRSAM
- `dist < 8km`   → Phalanx CIWS (auto-fires every 350ms)

### BrahMos Terminal Behaviour

BrahMos-II cruises at 10m altitude (sea-skimming). In the final 40km it executes a **pop-up maneuver** to 3.5km then dives onto the target - mimicking real-world BrahMos terminal phase that defeats radar horizon masking.

```javascript
if (dist < 40) {
  const popFrac = (40 − dist) / 40;
  t.pos.y = 0.01 + Math.sin(popFrac × Math.PI) × 3.5;
}
```

---

## 🛡️ Defence Systems (13 Total)

### International Systems
| System | Layer | Speed | Kill R | Range | Special |
|---|---|---|---|---|---|
| Phalanx CIWS | L4 | ∞ | 0.5km | 8km | Auto-fires, no interceptor |
| NASAMS | L3 | 20km/s | 5km | 40km | Fast reload |
| Iron Dome | L3 | 14km/s | 6km | 70km | Short-range swarms |
| Patriot PAC-3 | L2 | 16km/s | 6km | 90km | Cruise + ballistic |
| S-400 Triumph | L2 | 18km/s | 8km | 130km | Area denial |
| THAAD | L1 | 22km/s | 10km | 200km | Theatre BMD |
| Arrow-3 | L1 | 26km/s | 12km | 250km | Exo-atmospheric |
| Laser DEW | DEW | Light | ∞ | 22km | 22% energy/shot, recharges |

### 🇮🇳 Indian Indigenous Systems (DRDO)
| System | Layer | Speed | Range | Programme |
|---|---|---|---|---|
| QRSAM | L3 | 16km/s | 40km | Quick Reaction SAM |
| Akash-NG | L2 | 18km/s | 70km | Next-Gen Akash |
| Barak-8 MRSAM | L2 | 22km/s | 100km | DRDO + IAI joint |
| AAD Endo-AD | L1 | 19km/s | 100km | Endo-atmospheric BMD |
| PAD Prithvi AD | L1 | 24km/s | 200km | Exo-atmospheric BMD |

---

## 🎯 Threat Library (17 Types)

### Drones
| Platform | Speed | Altitude | RCS | Special |
|---|---|---|---|---|
| Shahed-136 | 0.8km/s | 400m | 0.35m² | Stochastic maneuvering |
| Swarm Drone | 0.7km/s | 300m | 0.15m² | Coordinated group AI |

### Cruise & Sea-Skimmers
| Platform | Speed | Altitude | RCS | Special |
|---|---|---|---|---|
| Tomahawk LACM | 2.5km/s | 120m | 0.25m² | - |
| Sea-Skimmer ASM | 3.0km/s | 15m | 0.18m² | Near radar-invisible |
| BrahMos-II 🇮🇳 | 3.5km/s | 10m | 0.08m² | Pop-up terminal dive |
| BrahMos-ER 🇮🇳 | 3.2km/s | 4km | 0.10m² | Extended range |

### Ballistic
| Platform | Speed | Altitude | RCS | Special |
|---|---|---|---|---|
| Iskander SRBM | 5.5km/s | 50km arc | 0.20m² | - |
| Prithvi-II 🇮🇳 | 4.5km/s | 45km arc | 0.22m² | - |
| MIRV Bus | 5.0km/s | 60km arc | 0.22m² | Splits at apogee → 3–5 RVs |

### Fighter Jets
| Platform | Speed | Altitude | RCS | Special |
|---|---|---|---|---|
| Su-57 Felon | 3.2km/s | 7km | 0.60m² | Evasive maneuver |
| F-35 Lightning II | 2.9km/s | 9km | 0.001m² | ECM + extreme stealth |
| F-22 Raptor | 3.8km/s | 11km | 0.0001m² | Barrel-roll evasion |
| J-20 Dragon | 3.3km/s | 10km | 0.002m² | ECM |
| MiG-31 Foxhound | 4.5km/s | 14km | 0.55m² | High-speed dash |
| B-21 Raider | 2.8km/s | 11km | 0.05m² | Extreme stealth |

### Hypersonic
| Platform | Speed | Altitude | RCS | Special |
|---|---|---|---|---|
| Kinzhal ASM | 9.5km/s | 18km arc | 0.15m² | - |
| Avangard HGV | 13km/s | 25km glide | 0.12m² | Random lateral maneuvers |
| AGM-88 HARM | 3.0km/s | 5km | 0.12m² | Homes on radar → 20s blackout |

---

## ⚡ Electronic Warfare System

### Jamming
- Reduces detection probability by up to **70%**
- Generates **ghost blips** on radar scope (purple, random positions)
- Radar sweep phosphor trail dims
- EW status bar appears at top of screen

### ARM Strike (AGM-88 HARM)
- Missile homes on radar installation coordinates
- On impact: **radar goes offline for 20 seconds**
- Radar scope shows "RADAR OFFLINE" with countdown
- After 20s, radar auto-repairs and resumes tracking

### ECM (Stealth Jets)
- F-35, F-22, J-20 toggle ECM periodically (3–8 second cycles)
- While active: RCS drops to **10% of normal**
- Engine glow turns magenta as visual indicator
- Pk drops by 35% against ECM-active targets

---

## 🌐 Warfare Scenarios (8 Total)

| Key | Scenario | Threats |
|---|---|---|
| `[1]` | Drone Swarm | 8 coordinated swarm drones |
| `[2]` | Gulf War Simulation | Ballistic, cruise, F-35, jets, drones |
| `[3]` | Hypersonic Barrage | HGV + Kinzhal + Iskander |
| `[4]` | MIRV Strike | 3 MIRV buses → 9–15 RVs |
| `[5]` | EW Blackout + Strike | ARM → radar down → stealth jets |
| `[6]` | Compound Assault | All threat types simultaneously |
| `[7]` | Multi-Vector Coordinated | 10 simultaneous diverse threats |
| `[8]` | Anti-Radiation Strike | ARM + F-35 + stealth package |
| `[9]` | BrahMos Supersonic Strike 🇮🇳 | BrahMos-II/ER + Prithvi-II |
| `[0]` | India-Pakistan Conflict 🇮🇳 | Prithvi, BrahMos, MiG-31, F-35, ARM |

---

## ✨ Visual Systems

### 3D Rendering
- **Three.js r128** with WebGL 2.0
- **PBR Materials** (`MeshStandardMaterial`) - roughness, metalness, emissive per object
- **ACES Filmic** tone mapping for cinematic colour grading
- **PCF Soft Shadow Maps** on terrain (2048×2048)
- **Noise-based terrain** with vertex colour blending (dirt → grass → rock by elevation)
- **Base zone flattening** - terrain procedurally smoothed around command centre

### Atmosphere
- **Exponential fog** (`FogExp2`) - real atmospheric depth
- **Layered haze cylinders** at 50m / 115m / 180m altitude
- **4000-star field** with colour-varied star points
- **12 perimeter lamps** around base fence

### Explosion System
- **3-ring shockwave** - fireball ring (orange), pressure wave (white), outer blast (red)
- **130-particle debris field** with gravity for major kills
- **Per-particle colour interpolation** from bright core to dark smoke
- **Flash point light** that fades with explosion

### Laser DEW Visual
- **Line geometry beam** from emitter to target with `opacity:1` flash
- **Full-screen purple tint** flash on fire
- **Energy bar** drains 22% per shot, recharges at 1.8%/s

---

## 📡 Radar Scope

Military-authentic PPI (Plan Position Indicator) scope with:

- **Phosphor persistence** - 40-frame sweep trail with natural fade
- **IFF symbols** - `+` cross for ballistic, `◇` diamond for aircraft, `●` dot for cruise/drone
- **Ghost blips** - purple random blips during jamming (EW confusion)
- **ECM dim** - blip brightness reduced when target ECM active
- **RADAR OFFLINE** overlay with countdown timer when ARM hits
- **Blake SNR** - blip size scales with target RCS (stealth barely visible)
- **Compass** N/E/S/W bearing markers
- **Selection ring** - double dashed ring on locked target

---

## 🔊 Audio System

Procedurally synthesised via **Web Audio API** - zero audio files:

| Event | Synthesis |
|---|---|
| Radar sweep | Oscillator + exponential frequency ramp |
| Missile launch | Sawtooth wave + white noise burst |
| Explosion | Noise buffer + sub-bass oscillator (55Hz → 18Hz) |
| Alert | Square wave multi-tone siren |
| EW jamming | Sine wave glide (220Hz → 880Hz → 110Hz) |

---

## 🛠 Tech Stack

```
Runtime        Three.js r128       3D scene graph, PBR materials, shadows
Rendering      WebGL 2.0           Hardware GPU acceleration
Physics        Vanilla JS          APN guidance, ballistics, MIRV, BrahMos
Audio          Web Audio API       Procedural synthesis, zero files
EW Engine      Custom JS           Jamming, ARM, ECM, ghost blips
Fonts          Google Fonts        Orbitron, Share Tech Mono
Deployment     GitHub Pages        Single HTML file, zero config
```

**No build tools. No webpack. No npm. No frameworks. Pure browser-native.**

---

## 📁 Single-File Architecture

The entire simulation (`index.html`) is organised into **23 numbered sections**:

```
§ 1   Defence System Definitions   ~40 lines   ← 13 ADS incl. Indian systems
§ 2   Threat Definitions           ~50 lines   ← 17 threats with full specs
§ 3   Simulation State             ~20 lines   ← Single SIM truth object
§ 4   Three.js Renderer            ~25 lines
§ 5   Camera Controller            ~55 lines   ← 3 modes
§ 6   Lighting + World             ~130 lines  ← Noise terrain, base, atmosphere
§ 7   Guidance Engine              ~35 lines   ← Augmented Lead Pursuit
§ 8   Physics Helpers              ~45 lines   ← Pk, detProb, threatScore
§ 9   AI Multi-Layer Assignment    ~30 lines   ← getBestADS() routing
§ 10  Mesh Factories               ~90 lines   ← PBR mesh construction
§ 11  Target Spawning + Update     ~130 lines  ← 17 flight models
§ 12  Interceptor System           ~95 lines   ← Launch, guidance, CIWS, Laser
§ 13  Laser DEW                    ~20 lines
§ 14  Explosions                   ~75 lines   ← 3-ring shockwave
§ 15  Radar Scope (Canvas 2D)      ~100 lines  ← IFF, ghost blips, ARM damage
§ 16  Detection System             ~35 lines   ← Blake + jamming + ARM
§ 17  Kill Effects                 ~20 lines   ← Screen-space projection
§ 18  Audio                        ~70 lines
§ 19  Log System                   ~10 lines   ← Military vocabulary
§ 20  UI Update                    ~110 lines  ← Flex layout, Pk bars, EW
§ 21  Controls                     ~70 lines   ← Keys [0-9][A][C][F][M][X]
§ 22  Main Loop                    ~65 lines   ← Radar repair, laser recharge
§ 23  Boot Sequence                ~45 lines   ← Auto Gulf War demo
```

---

## 🌟 Why This Project Stands Out

| Engineering Challenge | v5 Implementation |
|---|---|
| **Real guidance law** | Augmented Lead Pursuit, not "fly toward target" |
| **Detection physics** | Blake radar equation with ECM + jamming degradation |
| **Electronic warfare** | ARM kills radar; jamming creates ghost blips |
| **MIRV physics** | Bus destructs at apogee, spawns independent RVs |
| **Multi-layer AI** | 8-system layer routing by range, not one-size-fits-all |
| **CIWS autonomous** | Auto-fires on its own 350ms timer regardless of mode |
| **BrahMos behavior** | Sea-skimming + terminal pop-up maneuver modeled |
| **Indian systems** | 5 DRDO/indigenous ADS + 3 Indian threat types |
| **Zero dependencies** | Three.js CDN only - no npm, no build, no frameworks |
| **Single file** | Deploy by drag-and-drop to GitHub Pages |

---

## 🔭 Future Scope

- [ ] **ECEF coordinate system** - Real Earth geometry with WGS84
- [ ] **GLTF models** - Replace procedural meshes with real aircraft models
- [ ] **Multiplayer** - WebRTC cooperative defence with split-screen
- [ ] **Terrain heightmap** - Real DEM elevation data from NASA SRTM
- [ ] **Postprocessing bloom** - `UnrealBloomPass` for missile exhaust glow
- [ ] **Salvo doctrine** - Multiple interceptors per high-value threat
- [ ] **Mobile touch controls** - Pinch zoom, tap-to-engage
- [ ] **Real RCS database** - Import public radar cross-section data
- [ ] **Agni-V ICBM** - Indian ICBM with full exo-atmospheric trajectory
- [ ] **Naval mode** - INS Vikrant carrier group with Barak-8 naval variant

---

## 👔 For Recruiters

This project demonstrates:

**Systems Engineering** - Designed a real multi-layer IADS architecture (L1→L4) with intelligent routing, resource allocation, and engagement doctrine. Implemented genuine military guidance algorithms from mathematical specification.

**Graphics Engineering** - WebGL/Three.js pipeline with PBR rendering, procedural terrain, particle systems, screen-space projection, and Canvas 2D radar rendering at 60fps.

**Algorithm Implementation** - Augmented Lead Pursuit guidance, Blake radar equation, MIRV separation physics, BrahMos terminal maneuver, Pk estimation, AI threat scoring queue.

**Software Architecture** - ~1,900 lines of structured, documented, single-file JavaScript. 23 clearly separated modules. No framework dependency.

**Domain Knowledge** - Electronic warfare, radar physics, ballistic trajectories, Indian defence procurement (DRDO), multi-layer air defence doctrine.

---

## 📜 License

MIT - use freely, attribution appreciated.

---

*Built as a portfolio engineering project. Demonstrates simulation systems, defence algorithms, electronic warfare, and real-time 3D graphics in pure browser-native JavaScript.*