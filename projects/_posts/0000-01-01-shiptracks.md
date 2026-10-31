---
title: "Detecting ship-tracks"
excerpt: "Using machine learning to detect ship-tracks in satellite imagery and quantify how shipping emissions brighten clouds."
header:
  teaser: "/assets/images/invisible_tracks.png"
code: https://github.com/duncanwp/shiptrack-detection
data: https://catalogue.ceda.ac.uk/uuid/0d88dc06fd514e8199cdd653f00a7be0
tags:
  - aerosols
  - shallow clouds
  - satellite
  - machine learning
order: 5
status: earlier
---

Studied for many decades now, ship-tracks are a classic example of aerosol-cloud interactions. These perturbations to cloud albedo by aerosol emitted from ship exhaust are however very local and attempts to understand their relevance in terms of estimating global cloud droplet number and liquid water sensitivities flounder on the question of their representativeness. 

<span class="image"><img src="{{ 'assets/images/shiptracks.jpg' | relative_url }}" alt="Shiptracks in the North-East
 Atlantic" /></span>
 *Fig. 1: Clearly visible ship-tracks in the North-East Atlantic*

I have developed a deep convolutional neural network model trained on several existing hand-logged
 datasets to automatically detect these ship-tracks in observations and develop large-scale statistics about their
  prevalence and properties using MODIS imagery. You can browse the whole 20-year database right [here](shiptrack_map). 

### Changing emissions regulations

Using this database we found that the change in shipping regulations enforced by the International Maritime 
Organization in 2020 caused a marked reduction in tracks. As we describe in our recent [PNAS paper](https://www.pnas.org/doi/10.1073/pnas.2206885119), no such change is 
seen in other cloud properties and this constitutes the first clear evidence of a global response of clouds to environmental regulations.  

<span class="image inline img"><img src="{{ 'assets/images/shiptrack_timeseries.png' | relative_url }}"/></span>
*Fig. 2: Timeseries of global ship tracks  over the last 20 years showing a marked decline in 2020, coincident with a 
reduction in shipping emissions of SOx.*


### Invisible ship-tracks
Rather than searching for visible tracks in the clouds, in [Manshausen et al. 2022](https://www.nature.com/articles/s41586-022-05122-0) we used the locations of 
ships at a given time to determine where the tracks *should* be. Combining with remote sensing and weather data allowed us to 
measure the number of droplets and the amount of water in both polluted and unpolluted clouds. 

<span class="image inline"><img src="{{ 'assets/images/invisible_tracks.png' | relative_url }}" height="300"/></span>
*Fig. 3: Visible (left) and invisible (right) ship tracks. White boxes show where 
ship emissions interact with the 
clouds, and match well with the location of the visible tracks. Comparing these with the regions to either side 
(yellow) allows us to find the aerosol effect in invisible tracks.*

Surprisingly, we found that even when the ships do not leave visible tracks we can see 
that, on average, the amount of droplets increases in the polluted clouds, and these clouds also contain more water. 
This pattern is confirmed when comparing regions known for displaying ship tracks (the stratocumulus off Chile and 
Angola) with other regions, where they are less commonly seen (the cumulus clouds in the trades). 
 
Previous studies would have missed these stronger responses in the case of invisible tracks. 
From our observations, we estimate that the cooling that results from the increases in liquid water in 
clouds in response to aerosols much larger than was recently assessed by the IPCC AR6.

## Relevant papers
 - **Watson-Parris, D.**, Christensen, M., Laurenson, A., Clewley, D., Gryspeerdt, E., Stier, P. "Shipping regulations 
   lead to large reduction in cloud perturbations". *PNAS 119 (41) e2206885119*: <https://doi.org/10.1073/pnas.2206885119>
 - \*Manshausen, P., **Watson-Parris, D.**, Christensen, M., Jalkenen, J.P., Stier, P. "Invisible Ship Tracks Show 
   Strong Cloud Sensitivity to Aerosol". *Nature 610, 101–106*: <https://doi.org/10.1038/s41586-022-05122-0>
 - **Watson-Parris, D.**, Sutherland, S., Christensen, M., Caterini,
    A., Sejdinovic, D., Stier, P. "Detecting anthropogenic cloud
    perturbations with deep learning" *Climate Change: How Can AI Help?
    workshop at ICML 2019, Long Beach, California:*
    <https://arxiv.org/abs/1911.13061>  
