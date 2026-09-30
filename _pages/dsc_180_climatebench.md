---
permalink: /dsc_180_climatebench/
title: "DSC 180ab B03 - Week 3: ClimateBench in a week"
layout: single
toc: true
toc_sticky: true
---

This week you reproduce the core result of ClimateBench: the baseline emulators and their scores on the `ssp245` test scenario. You have the data pipeline from weeks 1–2 and an AI assistant, so the code is not the hard part. The hard part is making sure your numbers mean what you think they mean.

### Topics

- The emulation problem: learning a map from forcing trajectories to annual-mean climate fields
- Dimensionality reduction (EOFs/PCA) of inputs and outputs
- Pattern scaling, Gaussian processes, random forests, and CNN-LSTMs
- The ClimateBench evaluation metrics, and why the global term is weighted by 5
- A first look at JAX (`jit`, `grad`, `vmap`) and the Flax/Optax training stack

### Tasks

Split the four baselines across the team, but review each other's code. Everyone should understand all four.

1. **Pattern scaling.** Regress each grid cell's response on global-mean temperature, as in the paper. This is the reference point: anything more complex has to beat it. Look carefully at where the *test-time* global-mean temperature comes from in [`pattern_scaling_model.ipynb`](https://github.com/duncanwp/ClimateBench/blob/main/baseline_models/pattern_scaling_model.ipynb). What would change if you had to predict it from the inputs instead (for example with a simple energy-balance model)?
2. **Gaussian process.** Reduce the aerosol input maps to their leading EOFs (the baselines use 5 each for SO₂ and BC), fit one GP per target, and look at the predictive variance as well as the mean.
3. **Random forest.** Follow the paper's feature choices, and report the hyperparameters you end up with and how you chose them *without* touching `ssp245`.
4. **CNN-LSTM.** Use a 10-year input window, as in the paper. Train with several random seeds and report the spread.
5. **Metrics.** Implement the spatial, global, and total NRMSE over 2080–2100 against the ensemble mean of `ssp245`, with cos(latitude) area weighting. Regenerate the paper's results table (the [README leaderboard](https://github.com/duncanwp/ClimateBench#leaderboard) omits pattern scaling) for your models, and show a difference map (prediction − truth) for each model and target.
6. **One baseline in JAX.** Re-implement pattern scaling or a small neural network in pure JAX (or Flax + Optax), and check that you get the same scores as your first implementation. We will build on this in week 8.

**Tips**

- The official notebooks are in [`baseline_models/`](https://github.com/duncanwp/ClimateBench/tree/main/baseline_models), but try writing your own first, then compare. Differences between the two are where you learn the most.
- Normalise using training statistics only. Any statistic computed with `ssp245` included is leakage.
- If your scores are *better* than the paper's, be suspicious before you are pleased.

### Questions

Submit on the form linked in Slack before section.

1. Report your leaderboard next to the published one. For each model and target, say whether you reproduced the result, and if not, what the most likely cause is.
2. Which target is hardest to emulate, and why? Relate this to the physics (for example the signal-to-noise ratio, or the role of aerosols in DTR and precipitation).
3. How large is the ensemble (internal variability) spread in `ssp245` relative to your emulator errors? What does that imply about the best achievable NRMSE?
4. Which parts of the reproduction did an AI assistant get wrong, or nearly wrong, and how did you catch it?

### Presentation

Give a 5-minute replication presentation in section: your leaderboard, one figure you are proud of, and one thing that surprised you. You will build this into the final Phase I presentation in week 10.
