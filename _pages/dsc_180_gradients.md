---
permalink: /dsc_180_gradients/
title: "DSC 180ab B03 - Week 6: Differentiating a climate model"
layout: single
toc: true
toc_sticky: true
---

In weeks 4–5 you measured how the model responds to a change by running it twice and taking the difference. That is a *finite difference*. It works, but weather noise hides small responses, and you need a separate pair of runs for every change you want to test.

JCM is written in JAX, so it is *differentiable*: JAX can compute the exact derivative of any model output with respect to any input, directly from the model code. This week you will use those derivatives to ask how the atmosphere responds to its **boundary conditions**, such as sea-surface temperature (SST) and soil moisture, without running perturbation experiments.

## Forward mode and reverse mode

Think of the model as a function that maps inputs to outputs. For example, the inputs could be the SST at each of the roughly 3,000 ocean grid points, and the outputs could be the outgoing longwave radiation (OLR) at every grid point one day later. For small changes, the model behaves like a big matrix, the *Jacobian*, with one row per output and one column per input. JAX never builds this matrix, which would be huge. Instead, it can multiply by it in two directions:

- **Forward mode** (`jax.jvp`, a *Jacobian–vector product*) pushes one input pattern forwards through the model. You choose a perturbation, such as 1 K of warming over the tropical Pacific, and get the change in *every* output. Mathematically this is the matrix times your pattern, a *directional derivative*: a weighted sum of the columns for the grid points you warmed. Warming a single grid point picks out exactly one column. It costs about one model run per input pattern.
- **Reverse mode** (`jax.vjp`, or `jax.grad` for a single number, a *vector–Jacobian product*) pulls the sensitivity of one output backwards through the model. You choose one scalar target, such as global-mean OLR, and get its sensitivity to *every* input at once, as a map. This is a weighted sum of rows: global-mean OLR is an area-weighted average of the OLR at each grid point, so its map is the same average of those grid points' rows. A target that is a single output, such as the OLR at one grid point, picks out exactly one row. It costs a few model runs however many inputs there are, but it has to store the whole forward run, so its memory grows with the length of the run.

| You want | Use | Cost |
|---|---|---|
| The effect of one perturbation on many outputs | forward mode (`jax.jvp`) | about 1 model run per perturbation |
| The sensitivity of one output to many inputs | reverse mode (`jax.vjp`, `jax.grad`) | a few model runs in total, plus memory |

Weather centres call these the *tangent-linear* model and the *adjoint* model, and used to spend years writing them by hand. JAX builds both automatically.

One caveat matters a lot for climate. The atmosphere is chaotic, so small perturbations grow exponentially. Over a few hours, the derivative of a single run is a good guide to how the model responds. Over longer windows it keeps growing and stops resembling the averaged response we care about. This week, start with 6-hour windows and see how far you can push them.

## Readings

- From LEAP's online book [*Differentiable Earth System Modeling with JAX*](https://leap-stc.github.io/diff-earth/):
  - [1.2 First climate gradient](https://leap-stc.github.io/diff-earth/part1/first-climate-gradient.html): `jax.grad`, `jvp` and `vjp` on a one-line energy-balance model, always checked against a finite difference.
  - [2.1 Sensitivity maps](https://leap-stc.github.io/diff-earth/part2/sensitivity-maps.html): one reverse-mode pass gives a whole map.
  - Optional: [3.1 Gradients through chaos](https://leap-stc.github.io/diff-earth/part3/gradients-through-chaos.html), on why long windows go wrong.
- The JCM guide to [differentiating through JCM](https://github.com/climate-analytics-lab/jax-gcm/blob/dev/docs/source/gradients.rst) (skim it, especially the last section on gotchas).
- Optional: Errico (1997), [What is an adjoint model?](https://doi.org/10.1175/1520-0477(1997)078%3C2577:WIAAM%3E2.0.CO;2), the classic gentle introduction for atmospheric scientists.

## Setup

- Work on your DataHub GPU, in the same environment as weeks 4–5.
- LEAP ran a hackathon on JCM in October, and its walkthrough notebook, [`01_jcm_sensitivities.ipynb`](https://github.com/leap-stc/diff-earth/blob/main/2026_hackathon/notebooks/01_jcm_sensitivities.ipynb), is a good starting point. It was written for a slightly different JCM version, so some output names differ: for example, evaporation is `surface_flux.evap` in JCM 3.0, not `surface_flux.evap.0`. If a name isn't found, check `sorted(ds.data_vars)`.
- The first call to a model run, `jax.jvp` or `jax.vjp` compiles it, which takes a minute or so. Later calls with the same shapes are much faster, so time the second call, not the first.
- JAX uses single precision (float32) by default. Keep this in mind when you compare against finite differences.
- JAX computes the exact derivative of the model as coded. SPEEDY has on/off switches, such as the convection trigger, whose derivative is zero on either side of the switch, so a gradient cannot see a grid column crossing a threshold, but a finite difference can. JCM can smooth these derivatives through its `*_smoothing` parameters (see "Two gotchas" in the JCM gradients guide), which leave the model's values unchanged. Expect forward and reverse mode to agree closely with each other, but to differ from finite differences by tens of percent, more so over longer windows. Working out why is part of this week's work.

## Tasks

### 1. Warm up on a small model

Work through LEAP chapter 1.2, including its exercises. Make sure you can explain why forward and reverse mode give the same derivative here, and why the finite-difference error first falls and then rises as the step size gets smaller.

### 2. Run the LEAP JCM notebook

Run sections 1–2b of the hackathon notebook. Section 2b uses forward mode to estimate how evaporation responds to 1% drier soil everywhere. Compare it with the finite difference from section 2a. Why do the two differ in size?

### 3. How does OLR respond to SST?

This is the main experiment. The code below spins the model up, then defines one number, J: the global-mean OLR over a 6-hour window, as a function of an SST change at every ocean grid point. Everything else follows from differentiating J.

```python
from importlib.resources import files
import jax, jax.numpy as jnp, numpy as np
from jcm import load_states_from_xarray
from jcm.forcing import ForcingData
from jcm.model import Model
from jcm.physics.speedy.speedy_coords import get_speedy_coords
from jcm.terrain import TerrainData

coords = get_speedy_coords(spectral_truncation=31, layers=8)
data = files("jcm") / "data/bc/t30/clim"
terrain = TerrainData.from_file(data / "terrain.nc", coords=coords)
bc = ForcingData.from_file(data / "forcing.nc", coords=coords, align_mode="wrap_year")
model = Model(coords=coords, terrain=terrain, time_step=30)

# Spin up from rest, then start every experiment from the same state
spinup = model.run(forcing=bc, total_time=60, save_interval=60).to_xarray()
ic = load_states_from_xarray(spinup.isel(time=-1).sortby("level"))

ocean = jnp.asarray(np.asarray(terrain.fmask) < 0.5)        # (lon, lat), True over ocean
w = np.cos(np.deg2rad(spinup.lat.values))
weights = jnp.broadcast_to(jnp.asarray(w / w.mean()), ocean.shape)

WINDOW = 0.25  # days (6 hours)

def global_mean_olr(dsst):
    """Global-mean OLR (W m-2) averaged over WINDOW days, with SST + dsst over the ocean."""
    sst = bc.sea_surface_temperature
    forcing = bc.copy(sea_surface_temperature=sst.replace(values=sst.values + dsst * ocean))
    out = model.run(initial_state=ic, forcing=forcing, total_time=WINDOW,
                    save_interval=WINDOW, output_averages=True).to_xarray()
    return jnp.mean(weights * out["longwave_rad.ftop"].isel(time=-1).data)

zero = jnp.zeros(ocean.shape)

# Forward mode: the response of J to 1 K of warming over the whole ocean
J0, dJ_uniform = jax.jvp(global_mean_olr, (zero,), (jnp.ones_like(zero),))

# Reverse mode: the sensitivity of J to warming at every ocean grid point, in one pass
J0, backward = jax.vjp(global_mean_olr, zero)
(sensitivity_map,) = backward(1.0)
```

1. **Forward mode.** Report `dJ_uniform`, the change in global-mean OLR per kelvin of uniform ocean warming over 6 hours. Then estimate the same number by finite differences, `(J(+h) − J(−h)) / 2h` for uniform changes of h = 1 K and h = 0.1 K. How well do they agree?
2. **Reverse mode.** Plot `sensitivity_map`. Each value is the change in J per kelvin of warming at that one grid point, so it includes the grid cell's area weight. Divide by `weights` to get a map per unit area. Where does warming the ocean change OLR the most, and why might that be?
3. **Check the two modes against each other.** The map gives the response to *any* warming pattern as a sum: `jnp.sum(sensitivity_map * pattern)`. Check that it matches `jax.jvp` for the uniform pattern and for one regional box, such as the Niño-3.4 region (5°S–5°N, 170°W–120°W). This is the "dot-product test" used to check adjoint models.
4. **Cost.** Time one forward-mode call and one reverse-mode call. How many model runs would it take to build the same map from finite differences, one grid point at a time? How long would that take?

### 4. How long can the window be?

Repeat 3.1 and 3.2 for windows of 1, 2 and 4 days.

- How does `dJ_uniform` compare with the finite difference as the window grows?
- How does the size of the map (for example, `jnp.linalg.norm(sensitivity_map)`) change with window length? Does its pattern stay recognisable?
- Start from two or three different spun-up states (for example, spin-up lengths of 60, 70 and 80 days). At which window do the maps from different starting states stop agreeing?
- How do run time and memory grow with window length?

### 5. Choose your own

Pick one, and use the same recipe of building J, checking it against a finite difference, then making a map:

- **A regional target.** Change J to rainfall over a region you care about (for example, the Sahel or India) using `surface_exchange.precipitation` (in kg m⁻² s⁻¹), and map its sensitivity to SST.
- **A different boundary condition.** Replace the SST change with a soil-moisture field, one scaling factor per land grid point, as in LEAP's [challenge 2](https://github.com/leap-stc/diff-earth/blob/main/2026_hackathon/README.md#challenge-2-one-adjoint-pass-one-map). Where does soil moisture matter for evaporation or land temperature, and is any of it non-local?
- **Smoothing the switches.** Rebuild the model with nonzero `convection.trigger_smoothing` and `shortwave_radiation.cover_smoothing` (for example 0.05), and redo 3.1. Does the gap between the gradient and the finite difference shrink? If not, what else could explain it?
- **Parameters instead of boundary conditions.** Work through section 3b of the LEAP notebook, which uses reverse mode to get the sensitivity of land evaporation to five surface-flux parameters at once.

## Questions

Submit on the form linked in Slack before section.

1. In your own words, what is the difference between forward and reverse mode? Which would you use, and why, to get (a) maps of the OLR and rainfall response to warming one ocean patch, (b) the sensitivity of Sahel rainfall to SST everywhere, and (c) the sensitivity of one metric to five model parameters?
2. How well did the derivatives agree with finite differences? Give two reasons they might differ.
3. Show your SST sensitivity map and describe one physical feature of it.
4. What is the longest window you would trust a single-run gradient for, and what evidence supports that?
5. Warming in different ocean regions changes OLR by different amounts. What does that imply for the single climate feedback parameter you estimated in week 5?

## Looking ahead

In week 7 we will build an emulator of JCM itself over a small set of parameters, and compare what it tells us with the gradients from this week.
