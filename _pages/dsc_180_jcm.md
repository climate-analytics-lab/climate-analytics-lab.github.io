---
permalink: /dsc_180_jcm/
title: "DSC 180ab B03 - Weeks 4–5: Running JCM"
layout: single
toc: true
toc_sticky: true
---

So far you have emulated a climate model's output. Now you get to run one. [JAX-GCM (JCM)](/projects/jaxgcm) is a differentiable atmospheric general circulation model written in JAX. We will use its **SPEEDY** configuration: T31 resolution (96 × 48 grid, about 3.75°), 8 vertical levels, and simplified physics. It is cheap enough to run years of simulation on a single GPU.

One thing to understand from the start: in this configuration **sea-surface temperatures (SSTs) and sea ice are prescribed**. The land surface responds, but the ocean does not. That makes SPEEDY-JCM a natural tool for diagnosing *radiative forcing* and *feedbacks* separately, which are exactly the two quantities that set how much warming a given emission scenario produces (and which an emulator is implicitly learning).

## Week 4: Control climate

### Readings

- Read the [JCM paper](https://doi.org/10.5194/gmd-19-6451-2026) in full.
- The [SPEEDY physics notes](https://jax-gcm.readthedocs.io/en/latest/speedy_physics.html) in the JCM documentation, and skim Molteni (2003), [doi:10.1007/s00382-002-0268-2](https://doi.org/10.1007/s00382-002-0268-2), for the original model.
- The [getting started](https://jax-gcm.readthedocs.io/en/latest/getting_started.html) and [running at scale](https://jax-gcm.readthedocs.io/en/latest/running_at_scale.html) guides.

### Tasks

1. **First run.** Work through [`notebooks/01_jcm_demo.ipynb`](https://github.com/climate-analytics-lab/jax-gcm/blob/dev/notebooks/01_jcm_demo.ipynb) on a Casper GPU node, including the gradient cells at the end.
2. **Throughput.** Time a 1-year SPEEDY run on CPU and on GPU, separating compilation time from run time. Report simulated years per wall-clock hour. You will need this number to plan every later experiment.
3. **Control run.** Run the realistic present-day configuration (`python -m jcm.main +configuration=speedy-t31`, or the equivalent in Python with realistic terrain and climatological forcing) for at least 1 year of spin-up plus 10 years, saving monthly means. Record the exact configuration in your repo.
4. **Evaluate the climatology** against ERA5 (on Casper through the NCAR RDA) and against the NorESM2-LM historical climatology you used in weeks 1–2:
   - annual and seasonal (DJF/JJA) mean maps of near-surface temperature and precipitation, with bias maps and area-weighted pattern correlations
   - zonal-mean cross-sections of temperature and zonal wind (jets, Hadley cell)
   - the global-mean top-of-atmosphere (TOA) energy budget (incoming SW, reflected SW, OLR, and net)
5. **Variability.** Compute the interannual standard deviation of global-mean TOA net flux and surface temperature in your control. This sets how long the week 5 runs need to be to get a useful signal.

When working with the output, select vertical levels by coordinate value, not by index. See the note on vertical ordering in the JCM [`CLAUDE.md`](https://github.com/climate-analytics-lab/jax-gcm/blob/dev/CLAUDE.md), which applies to people too.

### Questions

1. What does SPEEDY get right about the present-day climate, and where are its largest biases? Suggest a physical reason for one bias.
2. Is your control in TOA energy balance? Should it be, given that SSTs are prescribed?
3. How does SPEEDY-JCM's cost per simulated year compare with a CMIP6 model like NorESM2? What kinds of experiment does that make possible?

## Week 5: Forcing and feedback

### Readings

- Forster et al. (2016), on diagnosing effective radiative forcing (ERF) with fixed-SST experiments: [doi:10.1002/2016JD025320](https://doi.org/10.1002/2016JD025320)
- Cess et al. (1990), on the +4 K uniform-SST method for diagnosing feedbacks: [doi:10.1029/JD095iD10p16601](https://doi.org/10.1029/JD095iD10p16601)
- Optional: Sherwood et al. (2020), sections 1–3, for the forcing–feedback framework and how it constrains climate sensitivity: [doi:10.1029/2019RG000678](https://doi.org/10.1029/2019RG000678)

### Tasks

Use the same prescribed SST/sea-ice climatology as your control for every run, so the only difference is the perturbation. Run each experiment long enough that the standard error of the global-mean TOA net flux is below about 0.1 W m⁻² (use your week 4 variability estimate).

1. **ERF of 4xCO₂.** Set `co2_vmr` to four times the control value and compute ERF as the change in global-mean TOA net flux relative to the control. Report the standard error.
2. **ERF as a function of CO₂.** Repeat for 0.5×, 2×, and 8× CO₂. Plot ERF against ln(C/C₀), and compare with the simplified expression 5.35 ln(C/C₀) W m⁻² (Myhre et al., 1998, [doi:10.1029/98GL01908](https://doi.org/10.1029/98GL01908)). Before you run it, read how `co2_vmr` enters SPEEDY's radiation in [`jcm/physics/forcing/speedy_forcing.py`](https://github.com/climate-analytics-lab/jax-gcm/blob/dev/jcm/physics/forcing/speedy_forcing.py) and write down what you expect to see.
3. **Cess +4 K.** Add 4 K uniformly to the prescribed SSTs (fixed CO₂ and sea ice). Compute the climate feedback parameter λ = ΔN / ΔT, where ΔT is the global-mean near-surface temperature change.
4. **Climate sensitivity.** Combine your 2×CO₂ ERF and λ into an estimate of equilibrium climate sensitivity, ECS ≈ −F₂ₓ / λ. Compare it with NorESM2-LM (about 2.5 K; Seland et al., 2020, [doi:10.5194/gmd-13-6165-2020](https://doi.org/10.5194/gmd-13-6165-2020)), and compare your CO₂ ERF with the NorESM2 ERF time series in ClimateBench's [`inputs_NorESM2_ERF.csv`](https://github.com/duncanwp/ClimateBench/blob/main/inputs_NorESM2_ERF.csv).
5. **Fast responses.** Map the change in land temperature and precipitation in the 4xCO₂ fixed-SST run. What is the global-mean precipitation change per W m⁻² of forcing?

### Questions

1. Why does a fixed-SST experiment isolate forcing from feedback? What does it miss (hint: think about what the land surface is doing)?
2. Is SPEEDY's CO₂ forcing logarithmic? Explain what you found in terms of the model code, and what it would mean for an emulator trained on SPEEDY output.
3. How does your ECS estimate compare with NorESM2-LM and with the IPCC AR6 assessed range? List the main approximations behind the number.
4. SPEEDY has no aerosol–radiation or aerosol–cloud interactions, but two of the four ClimateBench inputs are aerosols. What would you need to emulate NorESM2's full response with JCM? (Look at the ECHAM configurations in JCM with MACv2-SP aerosols.)

## Looking ahead

Week 6 will use `jax.grad` through these same experiments. For example, you will compute the sensitivity of the 4xCO₂ ERF or the Cess feedback to SPEEDY's cloud and convection parameters, check it against finite differences, and recover a perturbed parameter in a twin experiment (see [`notebooks/02_optimization_example.ipynb`](https://github.com/climate-analytics-lab/jax-gcm/blob/dev/notebooks/02_optimization_example.ipynb)). Keep your control and perturbation runs reproducible so you can reuse them.
