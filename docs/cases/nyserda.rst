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

    Locations of the NYSERDA floating lidar buoys and wind energy leased areas. (Image courtesy of Mike Optis.)

.. admonition:: Relevance to Wind Energy

   - Informs forecasting/operational modeling of wind farm conditions as to what surface forcings are important and at which scale they need to be modeled
   - Informs resource assessment strategies in the offshore environment
   - Impacts on Uncertainty Quantification (UQ)

.. admonition:: MMC Techniques Demonstrated

   - Ensemble mesoscale modeling and assessing best performers + model sensitivity
   - Stochastic cell perturbation method with Eckert scaling on LES domains 

Model Setups
------------

This setup utilizes 5 inline one-way nests with ∆x,y starting from 6.25 km and nesting down with ratios of 5:1 to 10 m (:numref:`fig-NYSERDA-domains`).
Domains 1 and 2 are both mesoscale domains while domains 3-5 are LES.
The vertical grid is consistent across all domains with 131 levels.
Below 400 m, the average ∆z is 10 m.
Above this point, ∆z stretches to around 420 m at model top.

  .. _fig-NYSERDA-domains:
  .. figure:: ../img/ModelSetup.png
    :width: 600
    :align: center

    WRF domain configuration for (a) domains 1-3 and (b) domains 3-5.

.. admonition::
   WRF Setup Available

   The WRF setup (including namelists and auxiliary input data) is available on the `WRF-setups
   repository of the A2e-MMC GitHub <https://github.com/a2e-mmc/WRF-setups/tree/master/NYSERDA>`_.

Additionally, a setup script is available in which the case will be setup automatically on a user's local environment.
This allows for easy recreation of the model simulations from this study.

Data Sources
------------
Sea surface temperature data is freely available for download at: https://podaac.jpl.nasa.gov/

The 10-minute averaged NYSERDA floating lidar data is freely available at: https://oswbuoysny.resourcepanorama.dnvgl.com/download/f67d14ad-07ab-4652-16d2-08d71f257da1

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
     - Cheyenne
     - 32 / 36
     - 1
     - ~10
   * - Meso-to-LES
     - WRF
     - Cheyenne
     - 32 / 36
     - 12
     - ~10


.. note::
   Meso-to-LES cases are computationally expensive. When all 5 domains are running, Cheyenne is able to get 20 minutes of simulation time in roughly 10 hours of wall clock. Thus, the LES simulation output is the combined output of 12 individual runs that are restarted every 20 minutes of simulation time resulting in a total of 4 hours of simulation.

Assessment
----------

.. admonition:: View/Download the Assessment Notebooks

   The assessment performed in this study is catalogued via Jupyter Notebooks on the `Assessment
   repository of the A2e-MMC GitHub
   <https://github.com/a2e-mmc/assessment/tree/master/studies/NYSERDA>`_.

This study utilizes several auxiliary SST datasets (:numref:`fig-NYSERDA-SST`) and surface parameterizations to determine model sensitivity of the low-level jet (LLJ) to surface temperature and surface characteristics such as roughness.
The SST datasets vary in resolution and fidelity which can be easily seen by examining the gradients of SST.
When on the LES domains, the overall differences are generally constrained to subtle gradients over the domain with a different mean SST.

  .. _fig-NYSERDA-SST:
  .. figure:: ../img/NYSERDA_SST.png
    :width: 500
    :align: center

    Auxiliary SST datasets utilized within this study.

The additional tests that are run include using WRF's SST skin parameterization, a 1-D ocean mixed-layer model (OMLM), implementing a shallow water roughness parameterization, and changing the land use dataset.
WRF's SST skin parameterization :cite:`zeng2005prognostic` prognostically calculates diurnal fluctuations in SST.
The 1-D OMLM model :cite:`zi2012new` adjusts SST based on the gradients of SST and other variables such as wind speed.
Lastly, the shallow water roughness scheme :cite:`jimenez2018need` calculates over-water roughness based on bathymetry (for depths between 10 and 100 m).

Results from the mesoscale simulations show that despite changing the SST dataset, there is very little change in the mean profile of the LLJ (:numref:`fig-NYSERDA-SST_ens` a) resulting in very low spread (:numref:`fig-NYSERDA-SST_ens` b).
Ensemble mean error also shows a consistent pattern between the SST datasets and auxiliary datasets with the lowest error near the surface and between the observed jet nose and simulated jet nose.

  .. _fig-NYSERDA-SST_ens:
  .. figure:: ../img/NYSERDA_SST_sensitivity.png
    :width: 400
    :align: center

    Vertical profiles of (a) mean wind speed and (b) ensemble mean error and spread for the mesoscale runs (d02) for the SST cases.

The same can be said for the auxiliary surface feature tests (:numref:`fig-NYSERDA-aux_ens`) in which the vertical profiles of wind speed are very similar for each case resulting in low spread.

  .. _fig-NYSERDA-aux_ens:
  .. figure:: ../img/NYSERDA_AUX_sensitivity.png
    :width: 400
    :align: center

    Vertical profiles of (a) mean wind speed and (b) ensemble mean error and spread for the mesoscale runs (d02) for the auxiliary tests.

.. attention::
  LES simulations are ongoing. This page will be updated upon completion.

.. _pubs:


References
----------

.. rubric:: Resulting Publications

.. bibliography:: ../all_project_pubs.bib
    :filter: mmc_rtd_section % "NYSERDA"

.. rubric:: Other

.. bibliography:: nyserda_refs.bib

