---
permalink: /dsc_180_ppe/
title: "DSC 180ab B03 - Week 7: Emulating JCM itself"
layout: single
toc: true
toc_sticky: true
---

Every climate model has dozens of uncertain parameters, such as how reflective clouds are or how humid air must be before convection starts. Modelling centres explore them with a *perturbed-parameter ensemble* (PPE): run the model many times with different parameter values, then fit an emulator to the results so they can predict the model's behaviour for any parameter values without running it. This is the same emulation idea as ClimateBench, but the inputs are model parameters instead of emissions.

This week you will build a small PPE of JCM, emulate it, and compare what the emulator tells you with the gradients from week 6. The two answer the same question, "how does the climate change if I change this parameter?", in very different ways.

## Readings

- Watson-Parris et al. (2021), [Model calibration using ESEm v1.1.0](https://doi.org/10.5194/gmd-14-7659-2021), sections 1–3 and the first case study. This is the workflow you will follow.
- Hourdin et al. (2017), [The art and science of climate model tuning](https://doi.org/10.1175/BAMS-D-15-00135.1). Skim it for why tuning matters and how it is done in practice.
- Optional: LEAP book chapter [2.2 Calibration as optimization](https://leap-stc.github.io/diff-earth/part2/calibration.html), which calibrates a small model by gradient descent instead.

## Tasks

### 1. Choose parameters and outputs

Use three SPEEDY parameters and three global-mean outputs:

| Parameter | Meaning | Default | Suggested range |
|---|---|---|---|
| `shortwave_radiation.albcl` | Cloud albedo | 0.43 | 0.35–0.50 |
| `convection.rhbl` | Boundary-layer relative humidity that triggers convection | 0.90 | 0.80–0.95 |
| `convection.entmax` | Maximum entrainment into convective plumes | 0.5 | 0.3–0.7 |

The outputs are global-mean OLR (`longwave_rad.ftop`), net absorbed shortwave at the top of the atmosphere (`shortwave_rad.ftop`), and precipitation (`surface_exchange.precipitation`, in kg m⁻² s⁻¹; multiply by 86,400 for mm/day), each averaged over 30 days. Before running anything, write down which outputs you expect each parameter to affect, and in which direction.

### 2. Run the ensemble

Draw 20–30 parameter sets with a Latin hypercube (`scipy.stats.qmc.LatinHypercube`), plus the default values. Start every member from the same spun-up state as week 6, run for 40 days, and average the last 30, so the first 10 days let the model adjust to its new parameters. The code below reuses `coords`, `terrain`, `bc` and `ic` from the week 6 starter code:

```python
from jcm.physics.speedy.params import Parameters
from jcm.physics.speedy.speedy_terms import speedy_physics

p0 = Parameters.default()

def physics_for(albcl, rhbl, entmax):
    params = p0.replace(
        shortwave_radiation=p0.shortwave_radiation.replace(albcl=jnp.array(albcl)),
        convection=p0.convection.replace(rhbl=jnp.array(rhbl), entmax=jnp.array(entmax)),
    )
    return speedy_physics(parameters=params)

def run_member(albcl, rhbl, entmax):
    model = Model(coords=coords, terrain=terrain, time_step=30,
                  physics=physics_for(albcl, rhbl, entmax))
    out = model.run(initial_state=ic, forcing=bc, total_time=40,
                    save_interval=10, output_averages=True).to_xarray()
    last30 = out.isel(time=slice(1, None)).mean("time")
    w = np.cos(np.deg2rad(out.lat))
    return {name: float(last30[name].weighted(w).mean(("lon", "lat")))
            for name in ["longwave_rad.ftop", "shortwave_rad.ftop", "surface_exchange.precipitation"]}
```

Save the results to a file after each member, so a crash doesn't lose the whole ensemble. Each new set of parameters triggers a fresh compile, so expect about a minute per member on top of the run itself. Plot each output against each parameter.

### 3. Emulate the ensemble

Fit a Gaussian process to each output, using the three parameters as inputs (`sklearn.gaussian_process.GaussianProcessRegressor` is fine). Hold out 5 members and check that the emulator predicts them within its stated uncertainty. Then plot each output against each parameter, with the other two fixed at their defaults, showing the emulator's mean and uncertainty.

### 4. Compare the emulator with gradients

The emulator's slope at the default parameters is one estimate of each parameter's effect on the 30-day climate. A gradient through JCM is another.

1. Compute the derivative of 30-day global-mean OLR with respect to `albcl` at the default value, using `jax.jvp` through the 40-day run. Compare it with the emulator's slope.
2. Repeat with a window of only a few days. Which estimate agrees with the emulator better, and why? Connect your answer to what you found about window length in week 6.

### 5. Calibrate

Satellite observations from CERES put both global-mean OLR and absorbed shortwave at about 240 W m⁻². Use your emulator to find all the parameter combinations whose outputs fall within a few W m⁻² of these values, allowing for the emulator's uncertainty. ESEm calls this *history matching*. Plot the region of parameter space that survives. Is it a single point, a line, or a large volume? What does that say about how well these observations constrain the model?

## Questions

Submit on the form linked in Slack before section.

1. Which parameter matters most for each output? Did that match your predictions from task 1?
2. How good is your emulator, and where is it least certain?
3. When do the emulator slope and the JCM gradient agree, and when don't they? Which would you trust for a 30-day climate, and why?
4. How well do global-mean observations constrain these three parameters? What extra observations might help?
5. A PPE of a full climate model costs thousands of GPU-hours. Where could gradients make this cheaper, and where can't they replace it?
