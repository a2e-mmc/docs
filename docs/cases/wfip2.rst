*****
WFIP2 
*****


Case Overview
-------------
This study examines a well-observed cold air pool (CAP) case from the second Wind Forecast Improvement Project (WFIP2).
Cold pools frequently occur in the complex terrain of this region, the Columbia River Gorge.
These events can be challenging to forecast for numerical weather prediction models due to small-scale atmospheric processes.
Here, we focus on a 10-day case study from 10-20 January 2017 because the time period encapsulates the full evolution of the CAP: development, persistence, and erosion.

A main focus of the study is to better understand the horizontal variability in meteorological and turbulence quantities during the CAP lifecycle.
To highlight these aspects of the event, we use ground-based in-situ and remote sensing measurements, in addition to the Weather Research and Forecasting (WRF) model.
We conduct high-resolution mesoscale (sub-kilometric) simulations using various planetary boundary layer (PBL) parameterizations.
The impact of the WRF PBL parameterizations on the modeled horizontal variability in meteorological and turbulence quantities will be presented.
Of particular interest is the performance of a three-dimensional PBL scheme that was recently implemented into WRF :cite:p:`Kosovic2020,Juliano2021,Eghdami2022`.
Additional details about this WFIP2 CAP study may be found in the original report by :cite:t:`Arthur2022`.

.. admonition:: Relevance for Wind Energy

   - Informs low wind energy production scenarios in complex terrain during stable conditions
   - Highlights the need for more comprehensive PBL parameterizations to reduce wind prediction errors

.. admonition:: MMC Technique Demonstrated

   - Application of a novel three-dimensional PBL scheme developed by NCAR to improve wind forecasting


Model Setup
------------
This study utilizes two nested domains (one-way coupling) with ∆x,y = 3 km in the outer domain (d01) and 750 m in the inner domain (d02) (:numref:`fig-WFIP2-domains`).
Domains 1 and 2 are both mesoscale domains, i.e. they both use a PBL parameterization.
The vertical grid is consistent across both domains with 50 levels and ∆z ≈ 15 m near the surface before stretching above.

  .. _fig-WFIP2-domains:
  .. figure:: ../img/wfip2_domain.png
    :width: 600
    :align: center

    WRF domain setup for the 10-day CAP event: HRRR (d01) and HRRRNEST (d02) are shown in the left and right panels. In d02, the locations of various WFIP2 measurement sites are shown by the red markers. This image is reproduced from :cite:t:`Arthur2022` (their Fig. 1).

.. attention::
  **The WRF setup has not been uploaded to GitHub yet. This is expected soon...**

The outer domain is initialized using the operational Rapid Refresh (RAP) model.
Reforecast simulations using the HRRR and HRRRNEST setup are conducted every 12 hours from 00 UTC 10 January 2017 through 12 UTC 19 January 2017 and integrated for 24 hours each.
The WRF configuration (including input, boundary, and namelist files) is available through the U.S. Department of Energy (DOE) Data Archive and Portal (DAP; http://a2e.energy.gov/data).


Sensitivity Tests
------------
Here, we focus on two WRF configurations from :cite:t:`Arthur2022`, which are listed in :numref:`table-1`.

.. _table-1:
.. list-table:: Turbulence mixing/horizontal diffusion treatments for Cases #1, 2, and 3. This table is reproduced from :cite:t:`Arthur2022` (their Table 1).
   :widths: 10 10 10
   :header-rows: 1

   * - Case #
     - d01
     - d02
   * - 1
     - MYNN PBL w/ 2D Smag. along coordinate surfaces
     - MYNN PBL w/ 2D Smag. along coordinate surfaces
   * - 2
     - MYNN PBL w/ 2D Smag. in physical space
     - MYNN PBL w/ 2D Smag. in physical space
   * - 3
     - MYNN PBL w/ 2D Smag. in physical space
     - 3D PBL w/ PBL Approx. in physical space

.. attention::
  **Case #1 is not emphasized in the present analysis because it is the same as Case #2, except it computes horizontal diffusion along coordinate surfaces. Our main intention here is to compare MYNN and 3D PBL when both compute horizontal diffusion in physical space.**

The only difference between Cases #2 and 3 is the treatment of vertical turbulent mixing and horizontal diffusion on d02: Case #2 uses the MYNN PBL scheme for 1D (vertical) turbulent mixing and the Smagorinsky-approach for 2D (horizontal) diffusion, while Case #3 uses the 3D PBL scheme w/ PBL approximation to handle both vertical and horizontal turbulent mixing.

Details about additional sensitivity simulations related to both the horizontal diffusion method (physical space versus along coordinate surfaces), in addition to specific components of the 3D PBL parameterization, are provided in :cite:t:`Arthur2022`.


Data Sources
------------
The surface-based observations from WFIP2 are freely available from the DOE DAP.
Please see :cite:t:`Arthur2022` (their Table 2) for detailed information about the WFIP2 instruments used in this study.

The WRF input, boundary, and namelist files for each reforecast period are also available through the DOE DAP.
The WRF outputs generated from this study and used in the analysis are available from the authors upon reasonable request.


HPC Runtime Information
-----------------------

.. list-table::
   :widths: 20 10 15 15 10 20
   :header-rows: 1
   :align: center

   * - Simulation
     - Codebase
     - HPC Name
     - Nodes/Procs
     - Runs
     - Time (hr/run)
   * - Mesoscale
     - WRF
     - LLNL LC
     - 4/36
     - 20
     - ~40


The WRF simulations are conducted using the Lawrence Livermore National Laboratory (LLNL) Livermore Computing (LC) resources.
Each WRF simulation is run every 12 hours for 24 hours of simulation time, resulting in a total of 20 simulations to cover the 10-day period.
Given the modest computing resources used (4 nodes x 36 cores = 144 processors) for the simulations, the wall clock to simulation time ratio is approximately 1.7 (40 hours wall clock to 24 hours simulation time).


Assessment
----------

.. admonition:: View/Download the Assessment Notebooks

   The assessment performed in this study is catalogued on the A2e-MMC GitHub here: https://github.com/a2e-mmc/assessment/tree/master/studies/WFIP2-CAP

An overview of the vertical structure of the cold pool event during the 10-day period as observed at the Wasco site is shown in :numref:`fig-wasco-10day`.
Specifically, we show the evolution of the measured potential temperature and wind speed profiles, as well the model bias from Case #3, which generally performs better than Case #2 on the whole, as will be shown below.

  .. _fig-wasco-10day:
  .. figure:: ../img/10_day_cold_pool.png
    :width: 500
    :align: center

    Measurements of (a) potential temperature and (c) wind speed at the Wasco WFIP2 site, in addition to the Case #3 model bias (b,d), for the 10-day CAP event. Results are shown for the first 12 hours (hours 3-15) of each reforecast period. This image is reproduced from :cite:t:`Arthur2022` (their Fig. 2). Contours of observed temperature (units in degrees Celsius) are included in (a,b) for reference.


The CAP developed on 13 January, as indicated by a deepening of the near-surface layer of relatively cool temperatures and calm winds.
These conditions persisted for about 6 days until 18 January, at which point relatively strong winds and warmer temperatures eroded the strong near-surface inversion.
During the CAP period, model Case #3 tends to have a cool bias adjacent to the surface and a warm bias above ~200 m above ground level (AGL).
Wind biases are generally positive in the lowest few 100s of m AGL and negative around 500-1000 m AGL.
The model biases are amplified during the cold pool erosion period, which has been reported previously as a challenge in this region :cite:p:`Wilczak2019,Olson2019`.


  .. _fig-wind-metrics:
  .. figure:: ../img/wind_error_metrics_all_stations.png
    :width: 500
    :align: center

    Event-averaged wind speed error metrics for each case shown in :numref:`table-1`. Results are shown for the (left) lowest 2.5 km AGL, (middle) lowest 1 km AGL, and (right) lowest 200 m AGL. Each observational site is shown individually, followed by an average over all five sites. The first 12 hours of each reforecast (i.e., hours 3-15) are used in the calculation. Note that because Cases #2 and 3 have the same setup on d01, their error metrics on d01 are identical. This image is reproduced from :cite:t:`Arthur2022` (their Fig. 4).


Two error metrics are computed for several WFIP2 sites across the entire 10-day CAP event: fractional bias (FB) and normalized absolute error (NAE). FB is computed as:

  .. math::

    FB_{\phi} = \frac{\overline{B_{\phi}}}{0.5(\overline{\phi_{WRF}}+\overline{\phi_{OBS}})}

and NAE is computed as:

  .. math::

    NAE_{\phi} = \frac{\overline{\lvert B_{\phi} \rvert}}{0.5(\overline{\phi_{WRF}}+\overline{\phi_{OBS}})}

where the overbar denotes an average over all available observations. :math:`FB_{\phi}` captures whether, on average, the model over- or underestimates the observation, while :math:`NAE_{\phi}` captures the average magnitude of the difference between the model and observation.
The error metrics for event-averaged wind speed are summarized in :numref:`fig-wind-metrics`.
Comparing Cases #2 and 3 , it is evident that the more comprehensive turbulence mixing parameterization (Case #3, 3D PBL) tends to produce the most accurate forecast at nearly every site and for both error metrics.


An important component of any turbulence kinetic energy (TKE)-based PBL parameterization is the computation of the eddy diffusivity, which dictates the strength of turbulent mixing and depends upon the magnitute of TKE.
To evaluate the performance of MYNN (Case #2) and 3D PBL (Case #3) with respect to TKE prediction, the bias is computed at Gordon's Ridge and shown in :numref:`fig-tke-bias`.


  .. _fig-tke-bias:
  .. figure:: ../img/tke_bias.png
    :width: 500
    :align: center

    Histograms of the TKE model bias values. Model bias is calculated for d02 using the first 12 hours of each reforecast (i.e., hours 3-15) and using observed TKE from the profiling lidar at Gordon's Ridge. Details about the TKE bias computation may be found in :cite:t:`Arthur2022`. This image is reproduced from :cite:t:`Arthur2022` (their Fig. 8).


The overall TKE model biases tend to be negative (underestimation in TKE) for both Cases #2 and 3.
However, the postitive biases are reduced substantially when using the 3D PBL parameterization.
The reason for this improvement in TKE prediction is primarily due to the different length scale formulations between MYNN and 3D PBL.
Further details regarding the observed and modeled turbulence characteristics from this CAP event are reported in :cite:t:`Arthur2022`.


Resulting Publications
----------

Arthur, R. S., Juliano, T. W., Adler, B., Krishnamurthy, R., Lundquist, J. K., Kosović, B., & Jiménez, P. A. (2022). Improved representation of horizontal variability and turbulence in mesoscale simulations of an extended cold-air pool event. *J. Appl. Meteor. Clim.* :cite:t:`Arthur2022`

Juliano, T. W., Kosović, B., Jiménez, P. A., Eghdami, M., Haupt, S. E., & Martilli, A. (2021). “Gray Zone” Simulations using a Three-Dimensional Planetary Boundary Layer Parameterization in the Weather Research and Forecasting Model. *Monthly Weather Review*. :cite:t:`Juliano2021`

Kosović, B., Munoz, P. J., Juliano, T. W., Martilli, A., Eghdami, M., Barros, A. P., & Haupt, S. E. (2020). Three-dimensional planetary boundary layer parameterization for high-resolution mesoscale simulations. In *Journal of Physics: Conference Series* (Vol. 1452, No. 1, p. 012080). IOP Publishing. :cite:t:`Kosovic2020`


References
----------

.. bibliography:: ../wfip2_bib.bib
   :all:
