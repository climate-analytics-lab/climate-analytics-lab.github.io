---
permalink: /dsc_180_diff_emulator/
title: "DSC 180ab B03 - Week 8: A differentiable ClimateBench emulator"
layout: single
toc: true
toc_sticky: true
---

Back to ClimateBench. In week 3 you built emulators that map emissions to climate. If that emulator is written in JAX, you can differentiate it too, and that opens up questions that a forward emulator alone can't answer easily. *Which* emissions, *where* and *when*, matter most for warming in a particular region? What is the smallest change to an emissions pathway that keeps warming below a target?

An emulator is cheap and smooth, so gradients through it are easy compared with JCM: no chaos and no switches. The hard part moves somewhere else, to whether the emulator's gradients mean anything physically. Everyone does tasks 1–3; then choose one of the options in task 4.

## Readings

- Leach et al. (2021), [FaIRv2.0.0](https://doi.org/10.5194/gmd-14-3007-2021), sections 1–2. This is the kind of simple climate model the IPCC uses to explore scenarios, and a natural global-mean core for a differentiable emulator.
- Re-read the ClimateBench paper's description of its inputs (cumulative CO₂ emissions, CH₄ emissions, and maps of SO₂ and black carbon emissions).

## Core tasks

### 1. A JAX emulator

Start from the JAX baseline you wrote in week 3. You need one pure JAX function,

```python
def emulate(co2, ch4, so2, bc):
    """ClimateBench inputs for one scenario -> tas, shape (year, lat, lon)."""
```

that scores within about 20% of your best week 3 emulator on `ssp245`. Two good options:

- **A pattern-scaling hybrid.** Predict global-mean temperature from the inputs (linear regression, or a small energy-balance model), then multiply by a learned warming pattern, plus a separate pattern for aerosols.
- **A small neural network** trained with Optax, like the ones from week 3.

Keep it simple. A model you understand completely is more useful this week than a slightly better one you don't.

### 2. Which emissions matter for regional warming?

Pick a region you care about, for example South Asia or the Sahel. Define J as the 2080–2100 mean of `tas` over that region under `ssp245`. Then use `jax.grad` to get its derivative with respect to all four inputs at once.

- Plot the sensitivity to CO₂ and CH₄ against year. When do emissions matter most for 2080–2100 warming?
- Sum the SO₂ sensitivity over years and map it. Where do SO₂ emissions matter most for warming in your region? Is any of it far away?
- Check one year and one grid box against a finite difference. The emulator is cheap, so this should agree almost exactly. Why is this so much easier than in JCM?

### 3. An inverse problem

Gradients let you work backwards from a target to an emissions pathway. Starting from `ssp245`, multiply CO₂ emissions after 2025 by a smooth scaling factor s(t), for example linear interpolation between values at 2030, 2050, 2070 and 2100. Remember that the ClimateBench CO₂ input is *cumulative*, so scale the annual emissions and then take a cumulative sum. Use Optax to find the s(t) that changes `ssp245` as little as possible while keeping 2081–2100 global-mean warming below 2 K. A penalty term on any warming above 2 K is enough.

- Plot the optimized pathway and its warming.
- Is the optimized scenario inside the range of the training scenarios, or is the optimizer exploiting the emulator somewhere it was never trained? How could you tell?

## 4. Choose one

This is the start of exploring towards Phase II. Pick one option, or propose your own in section.

- **Do emulators agree?** Repeat task 2 with a second, quite different emulator (for example pattern scaling and a neural network). Where their sensitivity maps disagree, which do you believe, and why?
- **Uncertainty in the answer.** Train 5 copies of your neural network with different random seeds. How much do the gradients in task 2, and the optimized pathway in task 3, vary between them?
- **Aerosols versus CO₂.** Cutting SO₂ improves air quality but removes some cooling. Optimize CO₂ and SO₂ scaling factors together. How does the cheapest route to 2 K change?
- **Precipitation.** Repeat task 2 with `pr` or `pr90` as the target. Are the gradients noisier or less physical than for temperature?

## Questions

Submit on the form linked in Slack before section.

1. Where and when do emissions matter most for warming in your region? Give a physical explanation.
2. Describe your optimized pathway. Would you trust it to inform policy? What would make you trust it more?
3. What did you find in your task 4 option, and what does it suggest about gradient-based scenario analysis?
4. Which part of this week would you most like to take further in Phase II?
