# Hail Formation Simulation

A browser-based 2D simulation of how hail forms inside a thunderstorm. It is written for people with an amateur
interest in meteorology, and it uses enough real physics to be useful as a visualization aid for scientists.

The simulation is split into four slides:

1. **Lifting**: warm, moist air rises in the updraft, condenses at cloud base, and becomes supercooled above the
   freezing level.
2. **Freezing & Accretion**: a zoomed-in view of one ice embryo collecting supercooled droplets. It shows dry
   (opaque rime) growth and wet (clear ice) growth.
3. **Layering**: turbulence and the storm's circulation carry stones through regions with different temperatures and
   amounts of cloud water. Click a stone to see its layered cross-section.
4. **Falling Hail**: stones become too heavy for the updraft, fall out, and melt below the freezing level. Small
   stones become rain and large ones reach the ground.

## Running

Open `index.html` in any modern browser. There is no build step and there are no dependencies.

## Controls

- **Prev / Next**, the slide tabs, or **← / →** switch slides. **Space** plays or pauses.
- **Animation position** scrubs the slide's timeline. The simulation is deterministic, so any position can be
  reproduced exactly.
- **Peak updraft**, **Surface temperature**, **Surface humidity** and **Turbulence** change the storm environment.
  Some slides add their own controls, which are shown in orange. Changing a setting re-runs the simulation up to the
  current position.
- **Show wind field** overlays the airflow (orange arrows are rising air, blue arrows are sinking air).

## Model summary

| Piece | Treatment |
| --- | --- |
| Temperature | Dry adiabatic (9.8 °C/km) below cloud base, a constant moist rate of 6 °C/km above (the real moist adiabat ranges from about 4 to 7+ °C/km) |
| Cloud base | LCL ≈ 125 m × (T − T<sub>d</sub>) |
| Airflow | Stream function, so the flow is divergence-free: a Gaussian updraft core with inflow, outflow and compensating subsidence, plus drifting turbulent eddy modes inside the cloud |
| Cloud water | Peaks in the core, is depleted toward −40 °C (glaciation), and is made patchy by entrainment |
| Fall speed | V<sub>t</sub> = √(4gDρ<sub>i</sub> / 3C<sub>d</sub>ρ<sub>a</sub>), with C<sub>d</sub> = 0.6 |
| Accretion | dD/dt = E·LWC·V<sub>t</sub> / (2ρ<sub>i</sub>), with E = 0.8 |
| Wet vs. dry growth | Surface heat balance: latent heat released vs. heat removed by convection and sublimation (Schumann–Ludlam limit) |
| Melting | dD/dt = −2h(T − 0) / (ρ<sub>i</sub>L<sub>f</sub>) below 0 °C, with meltwater shed |

Particles are drawn much larger than their true size, and the motion in the zoomed-in view is slowed down so it is
visible.

### Known simplifications

- The flow is 2-D and divergence-free (incompressible). Real storms satisfy density-weighted (anelastic)
  continuity and are three-dimensional.
- Ice nucleation uses a single onset temperature (−12 °C) and homogeneous freezing at −38 °C. Real
  ice-nucleating particles become active over a range of temperatures, from about −5 to −15 °C.
- Hail density (900 kg/m³) and drag coefficient (0.6) are held constant.
- In wet growth, stones keep all the water they collect, with no shedding and no spongy ice. This somewhat
  overestimates how fast large stones grow.
- Melting uses air temperature and ignores evaporative cooling, so the wet-bulb zero height is not represented.
- Cloud water is made patchy on purpose, to stand in for entrainment of dry air.

## Code layout

Everything lives in `index.html`:

- **Physics**: the `Storm` class (environment and airflow), `termVel`, `surfaceHeat` and `growHail`.
- **Scenes**: each entry in `SCENES` provides `init`, `step`, `draw` and `readouts`, plus the captions, legend and
  any slide-specific controls.
- **App**: a fixed-step timeline (`seek`), the control panel, and rendering.

To add a slide, such as one that runs every stage in sequence, append a new object to `SCENES` that reuses the
shared physics and drawing helpers.

## License

MIT
