---
title: Ship-track viewer
excerpt: "An interactive map of every ship-track detected by our machine learning algorithm, with MODIS timestamps for each track."
header:
  teaser: "assets/images/shiptracks_small.jpg"
data: https://doi.org/10.5281/zenodo.7038702
code: https://github.com/duncanwp/shiptrack-detection
learn_more: https://www.pnas.org/doi/10.1073/pnas.2206885119
tags:
  - aerosols
  - shallow clouds
  - satellite
order: 6
status: earlier
---

Easily browse all the ship-tracks detected in [Watson-Parris et al. 2022](https://www.pnas.org/doi/10.1073/pnas.2206885119) using our machine learning
algorithm. Each track as an associated MODIS timestamp so you can easily match with the underlying data. See the 
[ship-track detection](/projects/shiptracks) page for more details on their importance and effects. 

<div id="map">
    <div id="info-box">
        <p>Date: <input type="text" id="datepicker" class="date"></p>
        Info: <span id="info"></span>
    </div>
</div>
 
*Note*, these tracks have been simplified and compressed for easy browsing. They are also not always very obvious in RGB imagery shown above, but the detection algorithm uses a microphysical composite as described in the paper. If you need the exact tracks used in our analysis please see the links below. 

 

