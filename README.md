# Interstellar Black Hole — Geodesic Raytracer

A real-time, physically-motivated visualization of a Schwarzschild black hole
with a thin accretion disk, rendered in a single self-contained HTML file with
WebGL 1. No dependencies, no build step — just open `index.html`.

Live demo: <https://hushaohan.github.io/blackhole/>

## What it does

Every pixel fires a photon that is integrated along a **null geodesic** of the
Schwarzschild metric (RK2 midpoint, adaptive step size), so light bending,
the photon-sphere ring, the black-hole shadow, and the lensed image of the
far side of the disk folded over/under the shadow all emerge from the physics
rather than being faked.

### Physics in the shader

- **Geodesic integration** in the orbital plane of each ray — avoids the
  spherical-coordinate pole singularity entirely
- **Doppler beaming + gravitational redshift** on the disk: the asymmetry is
  hard-bounded (a linear blend of the Doppler factor, clamped) so the
  approaching side reads hotter without ever saturating to white, and the
  shift is expressed as *temperature* — boosted plasma trends white-hot gold,
  receding plasma deepens to ember. The palette never goes blue.
- **Shakura–Sunyaev temperature profile** (T ∝ r^-3/4) mapped through a
  blackbody color ramp; brightness peaks at the ISCO (r = 3 rs) and decays
  outward, so the near-camera edge of the disk stays dim and textured
- **Keplerian differential rotation** of turbulent dust, with bounded winding
  (epoch crossfading) so clouds shear without dissolving into threads
- **Wispy, semi-transparent outer disk edge** with front-to-back alpha
  compositing along each ray

### Scene dressing

- Three **world-space star shells** (r = 120/190/260) with true parallax,
  ~1/R² angular density, and rigid Keplerian drift (inner shells orbit
  faster, as anything bound that close must)
- Procedural nebula band and hash-grid starfield, plus a **Milky Way band**:
  a static great circle of ~3× denser, fainter stars and brighter nebulosity
- Two-octave bloom (half-res + quarter-res) with a bright-pass threshold,
  anamorphic flare with mirrored **lens ghosts**, exponential tonemapping
- **Volumetric haze** above/below the midplane giving the disk visible,
  internally structured thickness

## Controls

| Input | Action |
|---|---|
| Drag (mouse / one finger) | Orbit camera |
| Scroll / pinch | Zoom (3.5–45 rs) |
| Double-click | Reset view |

The camera auto-orbits gently when idle.

## Performance

- **Adaptive quality controller** targeting ~30 fps: the canvas stays at full
  (capped-DPR) resolution while internal render targets scale between 45% and
  100%; the HUD shows current fps, render resolution, and quality %
- ~1200 integration steps per ray max; rays terminate early on horizon hit,
  disk saturation, or escape

## Running locally

```sh
open index.html          # macOS
# or serve it:
python3 -m http.server 8000
```

Also deployed as a GitHub Page from `main` (root folder, no build).

## Files

- `index.html` — everything: shaders, renderer, camera, UI (~700 lines)
