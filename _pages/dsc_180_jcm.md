---
permalink: /dsc_180_jcm/
title: "DSC 180ab B03 - Weeks 4–5: Running JCM"
layout: single
toc: true
toc_sticky: true
---

So far you have emulated a climate model's output. Now you get to run one. [JAX-GCM (JCM)](/projects/jaxgcm) is a differentiable atmospheric general circulation model written in JAX. We will use its **SPEEDY** configuration: T31 resolution (96 × 48 grid, about 3.75°), 8 vertical levels, and simplified physics. It is cheap enough to run decades of simulation on a single GPU.

We will use it in two ways:

- **Week 4, atmosphere only (JCM).** Sea-surface temperatures (SSTs) and sea ice are prescribed, and only the atmosphere and land respond. This isolates the *effective radiative forcing* (ERF) of a perturbation.
- **Week 5, coupled to a slab ocean ([JAX-ESM](https://github.com/climate-analytics-lab/jax-esm), JEM).** JEM couples JCM to a mixed-layer ocean, land, and sea ice in one differentiable `jax.lax.scan`. The SSTs can now warm, so you can run CO₂ experiments to equilibrium and measure the *feedbacks* and *climate sensitivity* directly.

Forcing and feedback together set how much warming a given emission scenario produces, so they are exactly what a ClimateBench emulator is implicitly learning.

## Week 4: JCM and the fixed-SST world

### Readings

- Read the [JCM paper](https://doi.org/10.5194/gmd-19-6451-2026) in full. We will discuss it in section.
- The [SPEEDY physics notes](https://jax-gcm.readthedocs.io/en/latest/speedy_physics.html) in the JCM documentation, and skim Molteni (2003), [doi:10.1007/s00382-002-0268-2](https://doi.org/10.1007/s00382-002-0268-2), for the original model.
- Forster et al. (2016), on diagnosing ERF with fixed-SST experiments: [doi:10.1002/2016JD025320](https://doi.org/10.1002/2016JD025320)

### Tasks

1. **Install and first run.** Install JCM and JAX-ESM into your environment (`pip install jcm jax-esm`), then work through JCM's [`notebooks/01_jcm_demo.ipynb`](https://github.com/climate-analytics-lab/jax-gcm/blob/dev/notebooks/01_jcm_demo.ipynb) on your DataHub GPU, including the gradient cells at the end.
2. **Throughput.** Time a 1-year SPEEDY run on CPU and on GPU, separating compilation time from run time. Report simulated years per wall-clock hour. You will need this number to plan every later experiment.
3. **Control run.** Run the realistic present-day configuration (`python -m jcm.main +configuration=speedy-t31`, or the equivalent in Python with realistic terrain and climatological forcing) for at least 1 year of spin-up plus 10 years, saving monthly means. Record the exact configuration in your repo.
4. **Evaluate the climatology** against ERA5 monthly means (on Casper through the NCAR RDA; subset to the few variables you need and move the result to DataHub) and against the NorESM2-LM historical climatology you used in weeks 1–2:
   - annual and seasonal (DJF/JJA) mean maps of near-surface temperature and precipitation, with bias maps and area-weighted pattern correlations
   - zonal-mean cross-sections of temperature and zonal wind (jets, Hadley cell)
   - the global-mean top-of-atmosphere (TOA) energy budget (incoming SW, reflected SW, OLR, and net)
5. **Variability.** Compute the interannual standard deviation of global-mean TOA net flux in your control. Use it to choose run lengths that give a standard error below about 0.1 W m⁻².
6. **ERF as a function of CO₂.** Using the same prescribed SST/sea-ice climatology as the control, set `co2_vmr` to 0.5×, 2×, 4×, and 8× the control value. Compute ERF as the change in global-mean TOA net flux, with standard errors. Plot ERF against ln(C/C₀), and compare with 5.35 ln(C/C₀) W m⁻² (Myhre et al., 1998, [doi:10.1029/98GL01908](https://doi.org/10.1029/98GL01908)) and with the NorESM2 ERF time series in ClimateBench's [`inputs_NorESM2_ERF.csv`](https://github.com/duncanwp/ClimateBench/blob/main/inputs_NorESM2_ERF.csv). Before you run it, read how `co2_vmr` enters SPEEDY's radiation in [`jcm/physics/forcing/speedy_forcing.py`](https://github.com/climate-analytics-lab/jax-gcm/blob/dev/jcm/physics/forcing/speedy_forcing.py) and write down what you expect to see.
7. **Fast responses.** Map the change in land temperature and precipitation in the 4×CO₂ fixed-SST run. What is the global-mean precipitation change per W m⁻² of forcing?

When working with the output, select vertical levels by coordinate value, not by index. See the note on vertical ordering in the JCM [`CLAUDE.md`](https://github.com/climate-analytics-lab/jax-gcm/blob/dev/CLAUDE.md), which applies to people too.

### Questions

1. What does SPEEDY get right about the present-day climate, and where are its largest biases? Suggest a physical reason for one bias.
2. Why does a fixed-SST experiment isolate forcing from feedback? What does it miss (hint: think about what the land surface is doing)?
3. Is SPEEDY's CO₂ forcing logarithmic? Explain what you found in terms of the model code, and what it would mean for an emulator trained on SPEEDY output.
4. How does SPEEDY-JCM's cost per simulated year compare with a CMIP6 model like NorESM2? What kinds of experiment does that make possible?

## Week 5: Adding an ocean

### Readings

- The JEM [README](https://github.com/climate-analytics-lab/jax-esm#readme) and [getting started guide](https://github.com/climate-analytics-lab/jax-esm/blob/main/docs/source/getting_started.rst)
- Gregory et al. (2004), on estimating forcing and feedback by regressing ΔN against ΔT: [doi:10.1029/2003GL018747](https://doi.org/10.1029/2003GL018747)
- Optional: Sherwood et al. (2020), sections 1–3, for the forcing–feedback framework and how it constrains climate sensitivity: [doi:10.1029/2019RG000678](https://doi.org/10.1029/2019RG000678)

### Tasks

1. **Coupled first run.** Run the aquaplanet slab-ocean quick start (`python -m jem.main +configuration=aquaplanet-slab coupled_run=short_run`), then JEM's Earth-like configuration. Read the slab-ocean equation in `SlabOceanModel`. What sets the mixed-layer depth, and what does a Q-flux represent physically?
2. **Slab-ocean control.** Run the Q-flux slab control on realistic Earth boundary conditions for long enough to reach equilibrium (the slab's e-folding time is several years, so plan for a few decades). Check that its SST stays close to the prescribed climatology you used in week 4, and report any drift.
3. **Abrupt 2×CO₂ and 4×CO₂.** Starting from the control, run both until global-mean temperature stops rising. Plot global-mean near-surface temperature and TOA net flux against time.
4. **Gregory regression.** Regress annual-mean ΔN on ΔT for each run. The intercept estimates ERF and the slope estimates the feedback parameter λ. Compare the intercept with your week 4 fixed-SST ERF.
5. **Climate sensitivity.** Report ECS as the equilibrium ΔT in the 2×CO₂ run, and as −F₂ₓ/λ from the regression. Compare both with NorESM2-LM (about 2.5 K; Seland et al., 2020, [doi:10.5194/gmd-13-6165-2020](https://doi.org/10.5194/gmd-13-6165-2020)) and with the IPCC AR6 assessed range.
6. **Warming patterns.** Map the equilibrium ΔT and ΔP per kelvin of global warming, and compare them with the NorESM2 patterns from your week 3 pattern-scaling model. Where do they agree, and where don't they?

### Questions

1. Does ECS from the equilibrium run match −F₂ₓ/λ from the Gregory regression? If not, why not (think about whether λ is constant over the run)?
2. What does a slab ocean leave out compared with NorESM2's full ocean, and how would that affect transient scenarios like those in ClimateBench?
3. How do SPEEDY's warming and precipitation patterns compare with NorESM2's? What does that imply for using JCM–JEM runs as training data for an emulator of a CMIP6 model?
4. SPEEDY has no aerosol–radiation or aerosol–cloud interactions, but two of the four ClimateBench inputs are aerosols. What would you need to emulate NorESM2's full response with JCM? (Look at the ECHAM configurations in JCM with MACv2-SP aerosols.)

## Looking ahead

Week 6 will use `jax.grad` through these same experiments. For example, you will compute the sensitivity of the ERF or of the slab-ocean equilibrium warming to SPEEDY's cloud and convection parameters, check it against finite differences, and recover a perturbed parameter in a twin experiment. See JCM's [`02_optimization_example.ipynb`](https://github.com/climate-analytics-lab/jax-gcm/blob/dev/notebooks/02_optimization_example.ipynb) and JEM's [coupled-gradient example](https://github.com/climate-analytics-lab/jax-esm/blob/main/examples/01_basic/03_aquaplanet_response_to_SST_perturbation_using_gradient.ipynb). Keep your runs reproducible so you can reuse them.
