---
permalink: /dsc_180_reading/
title: "DSC 180ab B03 - Background reading"
layout: single
toc: true
toc_sticky: true
---

These are **optional** background papers that go with the weekly topics. The required readings are on each week's page. You don't need to read all of these: skim the abstract and figures, and read further if a paper connects to something you're working on. They are also a good place to start looking for Phase II ideas.

## Weeks 1–2: CMIP and scenarios

- Eyring, V., et al. (2016). Overview of the Coupled Model Intercomparison Project Phase 6 (CMIP6) experimental design and organization. *Geoscientific Model Development* 9, 1937–1958. [doi:10.5194/gmd-9-1937-2016](https://doi.org/10.5194/gmd-9-1937-2016)
  *Where the ClimateBench simulations come from, and why CMIP runs the experiments it does.*
- O'Neill, B. C., et al. (2016). The Scenario Model Intercomparison Project (ScenarioMIP) for CMIP6. *Geoscientific Model Development* 9, 3461–3482. [doi:10.5194/gmd-9-3461-2016](https://doi.org/10.5194/gmd-9-3461-2016)
  *What the SSP scenarios are, and how their emissions were designed.*

## Weeks 3–4: Emulators, and the model hierarchy

- Lütjens, B., Ferrari, R., Watson-Parris, D., & Selin, N. E. (2025). The impact of internal variability on benchmarking deep learning climate emulators. *Journal of Advances in Modeling Earth Systems* 17. [doi:10.1029/2024MS004619](https://doi.org/10.1029/2024MS004619)
  *Linear pattern scaling can outperform deep learning on ClimateBench once internal variability is accounted for. Read this before you trust your leaderboard.*
- Held, I. M. (2005). The gap between simulation and understanding in climate modeling. *Bulletin of the American Meteorological Society* 86, 1609–1614. [doi:10.1175/BAMS-86-11-1609](https://doi.org/10.1175/BAMS-86-11-1609)
  *A short essay on why intermediate-complexity models like SPEEDY are worth having.*
- Kochkov, D., et al. (2024). Neural general circulation models for weather and climate. *Nature* 632, 1060–1066. [doi:10.1038/s41586-024-07744-y](https://doi.org/10.1038/s41586-024-07744-y)
  *NeuralGCM: a hybrid ML–physics model built on the same Dinosaur dynamical core as JCM.*

## Weeks 5–6: Climate sensitivity, tuning, and differentiable models

- Knutti, R., Rugenstein, M. A. A., & Hegerl, G. C. (2017). Beyond equilibrium climate sensitivity. *Nature Geoscience* 10, 727–736. [doi:10.1038/ngeo3017](https://doi.org/10.1038/ngeo3017)
  *What ECS does and doesn't tell us, and why feedbacks change as the planet warms.*
- Hourdin, F., et al. (2017). The art and science of climate model tuning. *Bulletin of the American Meteorological Society* 98, 589–602. [doi:10.1175/BAMS-D-15-00135.1](https://doi.org/10.1175/BAMS-D-15-00135.1)
  *How climate models are calibrated in practice, and the problem gradient-based calibration is trying to improve.*
- Gelbrecht, M., White, A., Bathiany, S., & Boers, N. (2023). Differentiable programming for Earth system modeling. *Geoscientific Model Development* 16, 3123–3135. [doi:10.5194/gmd-16-3123-2023](https://doi.org/10.5194/gmd-16-3123-2023)
  *The case for automatic differentiation in climate models, and its pitfalls.*

## Weeks 7–8: Emulating models, and simple climate models

- Watson-Parris, D., Williams, A., Deaconu, L., & Stier, P. (2021). Model calibration using ESEm v1.1.0 – an open, scalable Earth system emulator. *Geoscientific Model Development* 14, 7659–7672. [doi:10.5194/gmd-14-7659-2021](https://doi.org/10.5194/gmd-14-7659-2021)
  *Emulating a climate model over its parameters, then using the emulator for calibration. This is the week 7 workflow.*
- Leach, N. J., et al. (2021). FaIRv2.0.0: a generalized impulse response model for climate uncertainty and future scenario exploration. *Geoscientific Model Development* 14, 3007–3036. [doi:10.5194/gmd-14-3007-2021](https://doi.org/10.5194/gmd-14-3007-2021)
  *The kind of simple climate model the IPCC uses for scenario assessment, and a natural global-mean component for a pattern-scaling hybrid.*
- Bonev, B., et al. (2023). Spherical Fourier neural operators: Learning stable dynamics on the sphere. *Proceedings of the 40th International Conference on Machine Learning (ICML)*. [arXiv:2306.03838](https://arxiv.org/abs/2306.03838)
  *A neural-operator architecture designed for data on the sphere, and a candidate architecture for Phase II.*

## Weeks 9–10: Physical constraints, and ML for climate more broadly

- Beucler, T., et al. (2021). Enforcing analytic constraints in neural networks emulating physical systems. *Physical Review Letters* 126, 098302. [doi:10.1103/PhysRevLett.126.098302](https://doi.org/10.1103/PhysRevLett.126.098302)
  *Two ways to make a neural network conserve energy and mass, and what each costs.*
- Watson-Parris, D. (2021). Machine learning for weather and climate are worlds apart. *Philosophical Transactions of the Royal Society A* 379, 20200098. [doi:10.1098/rsta.2020.0098](https://doi.org/10.1098/rsta.2020.0098)
  *Why climate emulation is a harder, and different, problem from weather forecasting.*
