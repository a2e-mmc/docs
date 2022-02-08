*******
NYSERDA 
*******

Case Overview
-------------

This study utilizes two floating lidars deployed by the New York State Energy and Research Development Authority (NYSERDA) in the New York Bight.
These buoys (E05 and E06 in :numref:`fig-NYSERDA-map`) have been deployed within wind energy leased areas with data freely available from the summer of 2019 through present.
The period of interest for this particular case is from April 4, 2020 at 06Z to April 6, 2020 at 06Z in which a persistent low level jet is observed at both buoys.
A targeted simulation period for the LES domains is on April 6 from 00Z to 06Z.

The goal if this study is to assess how model sensitivities to factors such as sea surface temperature (SST), sea surface roughness, and land use change across scales.
This is done by performing an ensemble of mesoscale-to-microscale simulations, calculating ensemble statistics and various metrics on each domain, and comparing how these metrics change between the mesoscale domains and microscale domains.

  .. _fig-NYSERDA-map:
  .. figure:: ../img/NYSERDA.png
    :width: 400
    :align: center

    Locations of the NYSERDA floating lidar buoys and wind energy leased areas.

Model Setups
------------

This setup utilizes 5 inline one-way nests with ∆x,y starting from 6.25 km and nesting down with ratios of 5:1 to 10 m.
Domains 1 and 2 are both mesoscale domains while domains 3-5 are LES.
The vertical grid is consistent across all domains with 131 levels.
Below 400 m, the average ∆z is 10 m.
Above this point, ∆z stretches to around 420 m at model top.

  .. _fig-NYSERDA-domains:
  .. figure:: ../img/ModelSetup.png
    :width: 600
    :align: center

    WRF domain configuration for (a) domains 1-3 and (b) domains 3-5.

.. attention::
  **The WRF setup has not been uploaded to GitHub yet. This is expected soon...**

The WRF setup (including namelists and auxiliary input data) is available on GitHub here: https://github.com/a2e-mmc/WRF-setups

Additionally, a setup script is available in which the case will be setup automatically on a user's local environment.
This allows for easy recreation of the model simulations from this study.

Validation Data Sources
-----------------------
The 10-minute averaged NYSERDA floating lidar data is freely available at: https://oswbuoysny.resourcepanorama.dnvgl.com/download/f67d14ad-07ab-4652-16d2-08d71f257da1

Assessment
----------

.. note:: 
  The assessment performed in this study is catalogued via Jupyter Notebooks on the A2e-MMC GitHub here: https://github.com/a2e-mmc/assessment/tree/master/studies/NYSERDA

Add some figures here..

blah
blah
blah

blah

blah


Resulting Publications
----------------------

.. attention::
  There are currently no publications for this project.







