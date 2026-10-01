---
permalink: /dsc_180_intro/
title: "DSC 180ab B03 - Weeks 1–2: Topic, paper, and data"
layout: single
toc: true
toc_sticky: true
---

Welcome! The first two weeks get everyone set up, cover the domain and the primary paper, and get you hands-on with the CMIP6 data behind ClimateBench. This compresses what used to take the first three to four weeks, so keep moving and ask early if you get stuck.

### Topics

- What is climate change, and how do we model it?
- What is CMIP and how does it relate to the IPCC?
- What is the ClimateBench dataset, and what exactly are its inputs and targets?
- Working with gridded climate data in xarray (area weighting, ensemble members, regridding)
- Reproducibility and replicability in data science

### Tasks

**Setup (week 1)**

- [ ] Log in to [DataHub](https://datahub.ucsd.edu) and start a GPU server
- [ ] Gain access to Casper (see the course home page) and confirm you can log in and start a small CPU job. You'll only need it for the raw-data tasks in week 2 and for ERA5 in week 4.
- [ ] Join the class Slack channel
- [ ] Create one team GitHub repository, with a `NOTES.md` decision log (see [Working with AI assistants](/dsc_180/#working-with-ai-assistants))
- [ ] Create a Python ≥3.11 environment with `xarray`, `dask`, `netCDF4`, `cartopy`, `scikit-learn`, `jax`, `flax`, and `optax`. Check that `jax.devices()` shows the GPU on DataHub. (You'll install the climate models themselves in week 4.)

**Readings (by the start of week 2 section)**

- Skim the latest UN Intergovernmental Panel on Climate Change [Synthesis Report](https://www.ipcc.ch/report/ar6/syr/downloads/report/IPCC_AR6_SYR_SPM.pdf) to get a summary of the latest climate change science, especially the figures.
- Fully read the ClimateBench [paper](https://agupubs.onlinelibrary.wiley.com/doi/full/10.1029/2021MS002954) by Watson-Parris et al.
- Pick two citations within the introduction to the ClimateBench paper and skim-read them (the abstract, the conclusions, and the figures, plus the methods if relevant). Each team member should choose different citations.

**Data (week 2)**

Do tasks 1–3 on DataHub, using the processed files from [Zenodo](https://doi.org/10.5281/zenodo.5196512). Tasks 4 and 5 need the raw CMIP6 output on Casper.

1. Plot maps of the 2005–2015 mean of each ClimateBench target (`tas`, `diurnal_temperature_range`, `pr`, `pr90`). Also plot the difference from 1850–1900 in the `historical` experiment, with appropriate colorbars, units, and titles.
2. Plot **area-weighted** global-mean time series of each target for all training experiments (`historical`, `ssp126`, `ssp370`, `ssp585`, `hist-GHG`, `hist-aer`) and `ssp245`. Show the spread across ensemble members.
3. Plot the four inputs (CO₂, CH₄, SO₂, BC) for each scenario. Which inputs are global scalars, and which are maps? How does cumulative CO₂ relate to the temperature time series in (2)?
4. **Regenerate one experiment from raw CMIP6.** On Casper, take `ssp245` (or another experiment) and rebuild the four targets from the raw NorESM2-LM daily `tas`, `tasmin`, `tasmax`, and `pr` on Casper (`/glade/collections/cmip/CMIP6/{activity}/NCC/NorESM2-LM/{experiment}`). One ensemble member is enough; keep the job small, since our core hours are limited. Then diff your result against the Zenodo file. Use the definitions in the paper and [`prepare_data.py`](https://github.com/duncanwp/ClimateBench/blob/main/prepare_data.py). Aim for agreement to within floating-point precision, and if you can't get there, explain why.
5. On Casper, each team member picks one additional CMIP6 variable (for example sea-level pressure, cloud cover, or TOA radiation) and produces the same plots for it.

### Questions

Submit your answers on the form linked in Slack before week 2 section.

1. What are the primary goals of the ClimateBench paper? What do you expect to be the major hurdles in reproducing it, and what would a successful Phase I reproduction look like?
2. Briefly explain each of the ClimateBench inputs, what it represents physically, and the relative magnitude of its climate impact. Why are CO₂ and CH₄ treated differently from SO₂ and BC?
3. Summarise each citation you read and how it relates to the ClimateBench paper. You will do this for each paper we read, so it is good to practise now.
4. Why does ClimateBench use `ssp245` as the test set, and which experiments would make it harder, or easier, for an emulator?
5. Describe one discrepancy you found between your regenerated data and the Zenodo version, or the checks that convinced you there wasn't one.

Be prepared to present your plots in section.
