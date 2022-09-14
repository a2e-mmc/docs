*****
SWiFT 
*****

The `Scaled Wind Farm Technology (SWiFT)
<https://energy.sandia.gov/programs/renewable-energy/wind-power/swift-facilities/>`_ facility in
Lubbock, Texas, is hosted at the Texas Tech University (TTU) National Wind Institute
:cite:p:`Hirth2014` and operated by Sandia National Laboratories. This site was previously chosen as
an International Energy Agency Task 31 ("Wakebench") `benchmark for wind-turbine-wake evolution and
dynamics <https://wakebench-swift.readthedocs.io/>`_. For MMC research, this site is an attractive
validation dataset because it has flat terrain with uniform land cover. Essential validation data
include a heavily instrumented 200-m meteorological mast with 10 measurements heights offering
high-frequency wind measurements from 0.9 to 200 m above ground level (AGL). These are complemented
by a radar wind profiler with radio acoustic sounding system (RASS) for measurements of wind and
temperature profiles above 200 m, as well as data from the West Texas Mesonet. Details of the site
characterization are provided in :cite:t:`Kelley2016`. 

  .. _fig-SWIFT-site:
  .. figure:: ../img/SWiFT_site.png
    :width: 600
    :align: center

    The SWiFT facility with adjacent TTU atmospheric facilities.


Case Overview
-------------

A classic diurnal cycle was identified between 8-9 November 2013 based on criteria outlined in
:cite:t:`MMCYear2Report`. Conditions were generally clear and considered quiescent. This diurnal
cycle featured a morning transition, daytime convective boundary layer, evening transition, and
nocturnal low-level jet (LLJ). The convective, neutral, and stable periods were separately assessed
for simulations with MYNN and YSU PBL schemes. Despite having a warm bias of up to ~5 K,
WRF captures the correct trends in wind speed, direction, and virtual potential temperature
:cite:p:`MMCYear2Report`.

A separate study into the effect of the *terra incognita* on modeling was conducted by
:cite:t:`Rai2019`. This study considered 3 cloud free days at SWiFT: 13 July 2013, 22 September
2013, and 6 June 2014. Proper Orthogonal Decomposition analysis clearly indicates that the
microscale contains energetic modes that originated from the mesoscale flow. Depending on horizontal
grid spacing and turbulence modeling choices, flow from the *terra incognita* may downscale into
unrealistic flow in the microscale. 

.. admonition:: Relevance to Wind Energy

   - Nonstationary conditions result in time-varying hub-height wind speed and direction, wind shear
     and veer, and turbulence intensity. 
   - Accurately capturing the downscaling of energy from the microscale is important for predicting
     realistic turbulent flow features in the wind-farm operating environment.

.. admonition:: MMC Techniques Demonstrated

   - Ensemble mesoscale modeling and assessing best performers + model sensitivity
   - Online (WRF/WRF-LES) and offline (WRF/SOWFA) coupling between NWP models and microscale LES
   - Internal coupling with two methods: the profile assimilation and mesoscale budget components
     approaches


Model Setups
------------

The WRF simulation setup contains 3 nested domains with 27 km, 9 km, and 3 km grid spacing, centered
at the SWiFT site (:numref:`fig-SWIFT-WRF-doms`). Simulations were started at 00:00 UTC on 8
November 2013. Initial and boundary conditions were set by the final analysis data from the Global
Forecast System. Turbulence modeling was provided by the MYNN Level 2.5 PBL scheme. Other physics
parameterization are detailed in the WRF namelist. 

  .. _fig-SWIFT-WRF-doms:
  .. figure:: ../img/SWiFT_WRF_domains.png
    :width: 400
    :align: center

    WRF domain configuration for SWiFT mesoscale simulation.

.. admonition::
   WRF Setup Available

   The WRF setup is available on the `WRF-setups repository of the A2e-MMC GitHub <https://github.com/a2e-mmc/WRF-setups/tree/master/SWiFT/20131108_GFS>`_.


Data Sources
------------

Two SWiFT datasets were used in the following MMC studies and are freely available on the `A2e Data 
Archive and Portal (DAP) <https://a2e.energy.gov/data#ProjectFilter=%5B%22mmc%22%5D>`_.

* 200-m tower :cite:p:`DAP_TTUtower`: The `analysis of this dataset 
  <https://github.com/a2e-mmc/assessment/blob/master/datasets/SWiFT/process_TTU_tower.ipynb>`_
  includes data standardization and sonic tilt correction, and calculates turbulence quantities of
  interest. `Additional calculations
  <https://github.com/a2e-mmc/assessment/blob/master/datasets/SWiFT/process_TTU_tower_heatflux_stability.ipynb>`_
  include virtual potential temperature, stability (bulk Richardson number and Obukhov stability
  parameter), and the surface heat flux. A more `indepth analysis of the TKE
  <https://github.com/a2e-mmc/assessment/blob/master/datasets/SWiFT/process_TTU_tower_TKE.ipynb>`_
  shows anomalous observations around 10:00 UTC on 9 November 2013.

  The mean quantities are combined with the radar dataset (described next) to form the input data
  for the :cite:t:`Allaerts2022` study. The turbulence quantities are used to validate the LES
  predicted turbulence in :cite:t:`Allaerts2020,Draxl2021,Allaerts2022`. 

* Radar wind profiler with RASS :cite:p:`DAP_TTUradar`: The `analysis of this dataset 
  <https://github.com/a2e-mmc/assessment/blob/master/datasets/SWiFT/process_TTU_radar.ipynb>`_ shows
  two wind scanning modes, short range (up to 2 km AGL) and long range (up to 6 km) and
  corresponding temperature profiles up to a maximum of 800 m AGL. The same notebook also performs
  the data reconstruction to create the mesoscale forcing dataset for the :cite:t:`Allaerts2022`
  study.

The SOWFA inputs were generated with the notebooks in the assessment repository:
`studies/SWiFT/coupling_comparison/preprocessing <https://github.com/ewquon/assessment/tree/master/studies/SWiFT/coupling_comparison/preprocessing/internal>`_.

Assessment
----------

* Results from the development and validation of the profile assimilation technique
  :cite:p:`Allaerts2020`, which couples the WRF mesoscale NWP model to SOWFA LES, are postprocessed
  in the
  `studies/SWiFT/profile_assimilation_wrf/produce_figures.ipynb <https://github.com/ewquon/assessment/blob/master/studies/SWiFT/profile_assimilation_wrf/produce_figures.ipynb>`_
  notebook. This study demonstrated that simple data assimilation techniques (i.e., direct profile
  assimilation) can lead to unphysical shear and turbulence production, due to the algorithm's
  inability to cope with inaccuracies in the mesoscale data. Applying mesoscale forcing with
  vertical smoothing (i.e., indirect profile assimilation) improves predictions of turbulence
  statistics (:numref:`fig-WRFPAT_TKE_comparison`). 

  .. _fig-WRFPAT_TKE_comparison:
  .. figure:: ../img/SWiFT_PAT_TKE_timehistory.png
    :width: 600
    :align: center

    Time history of turbulent kinetic energy at 80 m AGL for direct/indirect profile assimilation
    and budget components coupling, in comparison with the TTU meteorological tower; vertical dashed
    black lines indicate sunrise and sunset

* Results from the evaluation of coupling the WRF mesoscale NWP model to SOWFA through mesoscale
  budget components :cite:p:`Draxl2021` are postprocessed in the
  `studies/SWiFT/budget_components_coupling/plot_*
  <https://github.com/ewquon/assessment/tree/master/studies/SWiFT/budget_components_coupling>`_
  notebooks. This work shows that mesoscale models can have difficulties predicting profiles of
  shear and veer. While LES can improve shear and veer predictions, the wind speed and direction are
  not adjusted. When forcing the LES with the mesoscale budget, spatiotemporal averaging of the
  forcing terms is not necessary. 


References
----------

.. rubric:: Resulting Publications

..
    :labelprefix: swift-   
    :keyprefix: swift-   

.. bibliography:: ../all_project_pubs.bib
    :filter: mmc_rtd_section % "SWIFT"

.. rubric:: Other

.. bibliography:: swift_refs.bib

