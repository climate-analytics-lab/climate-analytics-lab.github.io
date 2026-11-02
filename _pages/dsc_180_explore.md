---
permalink: /dsc_180_explore/
title: "DSC 180ab B03 - Week 9: Explore and propose"
layout: single
toc: true
toc_sticky: true
---

This is the last working week of Phase I, and the most open. Rather than more set tasks, you will test one or two ideas for Phase II cheaply, so that you choose your project based on evidence rather than guesswork. By the end of the week your team should have a short written proposal and a 3-minute pitch.

Good Phase II projects usually start from something that surprised you in Phase I: a gradient that went wrong, an emulator that failed on one kind of scenario, or a result you didn't believe. Look back through your `NOTES.md` before you start.

## Tasks

### 1. Stress-test your emulator (half a day)

Before building on your week 8 emulator, check that it behaves physically in scenarios it was never trained on. Try at least two of these:

- Emissions stop entirely in 2030.
- Emissions stay fixed at their 2015 values.
- SO₂ and black carbon emissions are removed in 2030, with everything else as in `ssp245`.

Does the response have the sign, size and timescale you would expect? Note anything that looks wrong; it may be your Phase II project.

### 2. A feasibility probe (most of the week)

Pick a Phase II direction from the [ideas on the course page](/dsc_180/#phase-ii-ideas), or your own, and run the smallest experiment that tells you whether it is worth a quarter of work. Team members can probe different ideas. Aim for one clear figure. Some suggestions:

| Idea | A small probe | It looks promising if… |
|---|---|---|
| New emulator architectures | Train a small U-Net or Fourier neural operator on ClimateBench `tas` only, with a 1-hour training budget | It beats your week 3 baseline somewhere specific, such as regional patterns or aerosol-driven responses |
| Physically constrained emulators | Add a global-mean energy-balance layer to your network and test it on one of your task 1 scenarios | It behaves more sensibly out of distribution, at little cost in skill |
| Gradient-based scenario analysis | Extend the week 8 inverse problem with a regional target or an emulator ensemble | The answers are stable and physically interpretable |
| Longer-window gradients in JCM | Average week 6 gradients over many starting states (the "ensemble adjoint" of [Lea et al., 2000](https://doi.org/10.3402/tellusa.v52i5.12283)) and compare with finite differences | Averaging extends the window over which gradients agree with finite differences |
| Calibrating JCM | Use gradient descent on one or two week 7 parameters against CERES global means, then compare with your emulator's answer | Both approaches find the same region of parameter space, at very different costs |
| A JCM–JEM benchmark | Time a one-year coupled JCM–JEM run with rising CO₂, and estimate the cost of 20 scenarios of 100 years | It fits within your compute, and the result looks like a sensible climate |
| Decision support | Identify one user and one question, then sketch a Streamlit prototype on your week 8 emulator | You can state what the user would decide differently with your tool |

### 3. Write a proposal

Write a 1–2 page proposal as a team, covering:

1. **The question**, in one or two sentences, and why it matters (and to whom).
2. **Data and models.** What you will use, and whether you have access to it already.
3. **Approach.** What you will build or run.
4. **What success looks like.** A metric, a baseline to beat, or a figure you hope to make.
5. **Risks and a fallback.** What is most likely to go wrong, and a smaller version of the project that would still be worthwhile.
6. **What your probe showed**, with its figure.
7. **A plan for the first three weeks of winter quarter.**

Present a 3-minute pitch in section. We will use the feedback to finalize the proposals in week 10.

## Questions

Submit on the form linked in Slack before section.

1. What did your probe show, and how did it change your plans?
2. What is the biggest risk in your proposed project, and what is your fallback?
3. What do you need from us to get started: data, compute, or people to talk to?

Background papers for most of these directions are on the [reading list](/dsc_180_reading).
