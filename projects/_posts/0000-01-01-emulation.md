---
title: Climate model emulation
excerpt: "Developing climate model emulators for better parameter estimation and calibration."
header:
  teaser: "assets/images/emulator_schematic.svg"
  teaser_fit: contain
code: https://github.com/duncanwp/ESEm
data: https://doi.org/10.5281/zenodo.3856644
tags:
  - emulation
  - climate modeling
order: 8
status: earlier
---

Large computer models requiring hundreds of input parameters, taking thousands of core-hours to run
, and producing terabytes of output are ubiquitous in the earth sciences. As I discuss in my recent [perspective
 paper](https://royalsocietypublishing.org/doi/10.1098/rsta.2020.0098), it is becoming
 common practice to develop emulators as fast approximations of these models in order to explore the relationships between these inputs and outputs, understand uncertainties and generate large ensembles datasets. 

<span class="image left"><img src="{{ 'assets/images/emulator_schematic.svg' | relative_url }}" alt="A schematic diagram of 
emulation of two climate model parameters" /></span>

Primarily, I build emulators using Gaussian Process regression across different (uncertain) input parameters in
 order to allow fast sampling for likelihood-free inference. (Basically determining the parameter combinations which
  produce outputs which are compatible with the available observations.) I've used this approach for
   constraining parametric
   uncertainty in both the [direct](https://doi.org/10.1029/2020GL087141) and [first indirect
](https://doi.org/10.1073/pnas.1922502117)
 aerosol effects.
 
I developed [ESEm](https://gmd.copernicus.org/preprints/gmd-2021-267/) as an [open source](https://github.
com/duncanwp/ESEm) tool providing a 
general workflow for emulating and validating a
 wide variety of climate models and outputs. It is built on well established, high performance libraries to
  ensure robustness, extensibility and scalability. I've also been involved with the development of a novel neural [architecture search algorithm](https://arxiv.org/abs/2001.08055) for general emulation of
 physical simulations, which was [higlighted by Science magazine](https://www.sciencemag.org/news/2020/02/models-galaxies-atoms-simple-ai-shortcuts-speed-simulations-billions-times).

<span class="image middle"><img src="{{ 'assets/images/Figure_A2.png' | relative_url }}" alt="Climate fields as 
output by 
a full complexity climate model compared to those predicted by machine learning emulators" /></span>
 *Climate fields as output by 
a full complexity climate model compared to those predicted by machine learning emulators in ClimateBench.*

I have also been developing a Climate model emulation becnhmark dataset called [ClimateBench](https://github.
com/duncanwp/ClimateBench) designed for scenario exploration and inspired by [WeatherBench](https://github.
com/pangeo-data/WeatherBench). It consists of NorESM2 
simulation outputs with associated forcing data processed in to a consistent format from a variety of experiments 
performed for CMIP6. Benchmark models and evaluation criteria are described in the full [publication](https://doi.org/10.1029/2021MS002954). 

*New!* You can now interact with an online ClimateBench emulator [here](/projects/climatebench_app)! 
 
## Relevant papers
 - **Watson-Parris, D.**, Rao, Y., Olivié, D., Seland, Ø., ... "ClimateBench v1.0: A benchmark for data-driven climate projections". 
   *Journal of Advances in Modeling Earth Systems 14, e2021MS002954*: <https://doi.org/10.1029/2021MS002954>
 - **Watson-Parris, D.**, Williams, A., Deaconu, L., Stier, P. "Model
   calibration using ESEm v1.1.0 – an open, scalable Earth System
   Emulator". *Accepted at Geoscientific Model Development:*
   <https://doi.org/10.5194/gmd-2021-267>
 - **Watson-Parris, D.** "Machine learning for climate and weather are
    worlds apart". *Phil. Trans. R. Soc. A 379:* 
    <http://doi.org/10.1098/rsta.2020.0098>
 - McCoy, I. L., McCoy, D. T., Wood, R., Regayre, L., **Watson- Parris,
    D.**, Grosvenor, D. P., Mulcahy, J., Hu, Y., Bender, F. A. M.,
    Field, P. R., Carslaw, K., Gordon, H. "The hemispheric contrast in
    cloud microphysical properties constrains aerosol forcing".
    *Proceedings of the National Academy of Sciences 117 (32)
    18998-19006:* <https://doi.org/10.1073/pnas.1922502117>
 - **Watson-Parris, D.**, Bellouin, N., Deaconu, L., Schutgens, N.,
    Yoshioka, M., Regayre, L. A., Pringle, K. J., Johnson, J. S.,
    Carslaw, K. S. & Stier, P. "Constraining uncertainty in aerosol
    direct forcing". *Geophysical Research Letters, 47:*
    <https://doi.org/10.1029/2020GL087141>
 - Kasim, M. F., **Watson-Parris, D.**, Deaconu, L. ,
    Topp-Mugglestone, J. , Hatfield, P., Froula, D. H., Gregori, G.,
    Jarvis, M. , Korenaga, J., Viezzer, E. & Vinko S. M. "Accelerating
    simulations in science with deep neural architecture search".
    *Accepted at Machine Learning: Science and Technology:* <https://arxiv.org/abs/2001.08055>  
