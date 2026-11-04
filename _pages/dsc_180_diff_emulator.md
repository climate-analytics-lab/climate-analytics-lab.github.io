---
permalink: /dsc_180_diff_emulator/
title: "DSC 180ab B03 - Weeks 7–8: A differentiable ClimateBench emulator"
layout: single
toc: true
toc_sticky: true
---

Back to ClimateBench. In week 3 you built emulators that map emissions to climate. If that emulator is written in JAX, you can differentiate it too, and that opens up questions a forward emulator alone can't easily answer. *Which* emissions, *where* and *when*, matter most for warming in a particular region? What is the smallest change to an emissions pathway that keeps warming below a target?

An emulator is cheap and smooth, so gradients through it are much easier to compute than in JCM, with no chaos and no switches. The hard part moves somewhere else: do the emulator's gradients mean anything physically? You have two weeks for this, so take the time to understand what you find, not just to produce it. Each week ends with a short written reflection.

## Readings

- Leach et al. (2021), [FaIRv2.0.0](https://doi.org/10.5194/gmd-14-3007-2021), sections 1–2. This is the kind of simple climate model the IPCC uses to explore scenarios, and a natural global-mean core for a differentiable emulator.
- Re-read the ClimateBench paper's description of its inputs (cumulative CO₂ emissions, CH₄ emissions, and maps of SO₂ and black carbon emissions).
- Optional: LEAP book chapter [2.2 Calibration as optimization](https://leap-stc.github.io/diff-earth/part2/calibration.html), which uses the same Optax optimization loop you will use in week 8.

## Week 7: Build it and understand it

### 1. A JAX emulator

Start from the JAX baseline you wrote in week 3. You need one pure JAX function,

```python
def emulate(co2, ch4, so2, bc):
    """ClimateBench inputs for one scenario -> tas, shape (year, lat, lon)."""
```

that scores within about 20% of your best week 3 emulator on `ssp245`. Two good options:

- **A pattern-scaling hybrid.** Predict global-mean temperature from the inputs (linear regression, or a small energy-balance model), then multiply by a learned warming pattern, plus a separate pattern for aerosols.
- **A small neural network** trained with Optax, like the ones from week 3.

Keep it simple. A model you understand completely is more useful here than a slightly better one you don't.

### 2. A first gradient you can check against the IPCC

Let J be global-mean `tas` averaged over 2081–2100 under `ssp245`. Use `jax.grad` to get its derivative with respect to the CO₂ input in every year.

- The ClimateBench CO₂ input is *cumulative* emissions. Convert your gradient into the effect of emitting one extra unit of CO₂ in a given year, and plot it against year. (Hint: one extra unit emitted in year t raises the cumulative total in every year from t onwards.)
- The warming per unit of cumulative CO₂ emitted is the *transient climate response to cumulative emissions* (TCRE). The IPCC's best estimate is about 0.45 K per 1000 GtCO₂, with a likely range of 0.27–0.63 K. Estimate your emulator's TCRE from your gradient, checking the units of the input file, and compare.
- Check one year against a finite difference. The emulator is cheap and smooth, so this should agree almost exactly.

### 3. Which emissions matter for regional warming?

Pick a region you care about, for example South Asia or the Sahel. Define J as the 2081–2100 mean of `tas` over that region, and take its gradient with respect to all four inputs at once.

- Plot the sensitivity to CO₂ and CH₄ against year. When do emissions matter most for late-century warming? Why is CH₄ different from CO₂?
- Sum the SO₂ sensitivity over years and map it. Where do SO₂ emissions matter most for warming in your region? Is any of it far away?

### Reflection

Write half a page in your `NOTES.md`, and be ready to talk it through in section. In week 6 you took gradients through JCM; this week you took them through an emulator of NorESM2. What does each gradient actually represent? When would you trust one over the other? Use your own results as evidence.

## Week 8: Use it, and find its limits

### 4. An inverse problem

Gradients let you work backwards from a target to an emissions pathway. Starting from `ssp245`, multiply annual CO₂ emissions after 2025 by a smooth scaling factor s(t), for example linear interpolation between values at 2030, 2050, 2070 and 2100, then take the cumulative sum. Use Optax to find the s(t) that changes `ssp245` as little as possible while keeping 2081–2100 global-mean warming below 2 K. A penalty on any warming above 2 K is enough.

- Plot the optimized pathway and its warming. How does it relate to your TCRE estimate from task 2?
- Is the optimized scenario inside the range of the training scenarios, or is the optimizer exploiting the emulator somewhere it was never trained? How could you tell?

### 5. Stress-test your emulator

Check that your emulator behaves physically in scenarios it was never trained on. Try at least two:

- Emissions stop entirely in 2030.
- Emissions stay fixed at their 2015 values.
- SO₂ and black carbon emissions are removed in 2030, with everything else as in `ssp245`.

Does the response have the sign, size and timescale you would expect? What does that tell you about how far you can trust task 4? Note anything that looks wrong; it may be your Phase II project.

### 6. Choose one

This is the start of exploring towards Phase II. Pick one option, or propose your own in section.

- **Do emulators agree?** Repeat task 3 with a second, quite different emulator (for example pattern scaling and a neural network). Where their sensitivity maps disagree, which do you believe, and why?
- **Uncertainty in the answer.** Train 5 copies of your neural network with different random seeds. How much do the gradients in task 3, and the optimized pathway in task 4, vary between them?
- **Aerosols versus CO₂.** Cutting SO₂ improves air quality but removes some cooling. Optimize CO₂ and SO₂ scaling factors together. How does the cheapest route to 2 K change?
- **Precipitation.** Repeat task 3 with `pr` or `pr90` as the target. Are the gradients noisier or less physical than for temperature?

### Reflection

Write half a page on this question: what can gradient-based scenario analysis tell a decision-maker, and what can't it? Draw on tasks 4–6.

## Questions

Submit on the form linked in Slack before each section: questions 1–2 in week 7, and 3–5 in week 8.

1. How does your emulator's TCRE compare with the IPCC range? If it differs, why might that be?
2. Where and when do emissions matter most for warming in your region? Give a physical explanation.
3. Describe your optimized pathway. Would you trust it to inform policy? What would make you trust it more?
4. Which stress test surprised you most, and why?
5. What did you find in your task 6 option, and which part of these two weeks would you most like to take further in Phase II?
