****************
Numerical Models
****************


Weather Research and Forecasting (WRF) Model
============================================
WRF is a tool for numerical weather prediction developed at the National Center
for Atmospheric Research (NCAR), the National Oceanic and Atmospheric
Administration (NOAA), the former Air Force Weather Agency (AFWA), the Naval
Research Laboratory, the University of Oklahoma, and the Federal Aviation
Administration (FAA).

Running WRF in large-eddy simulation (LES) mode, nested within a mesoscale WRF
parent domain allows for online coupling between meso- and micro-scales.
WRF-LES implements a generalized actuator disk and generalized actuator line.

More information: https://www.mmm.ucar.edu/weather-research-and-forecasting-model

MMC-WRF
-------
Within the MMC project, several additions have been made to the WRF model for coupling and wind energy related processes.
Included in this version of WRF are several perturbation strategies, enhancements for running idealized cases, adjustments to the tslist functionality, and more.
This version is also tagged at various stages for particular studies.
If attempting to re-create results, please make sure you are running on the tagged version for the study of interest.

.. admonition:: Download MMC-WRF

   This version of WRF is freely available on GitHub: `MMC-WRF <https://github.com/a2e-mmc/WRF>`_

Simulator fOr Wind Farm Applications (SOWFA)
============================================
SOWFA is a set of CFD tools developed at the National Renewable Energy Laboratory (NREL) based on
the open-source OpenFOAM (Field Operation And Manipulation) platform for
simulating the microscale atmospheric boundary layer environment. Wind turbines
are modeled as actuator lines (or actuator disks) with the option to include
momentum sources to represent the wind turbine tower and nacelle. Coupling to
the NREL FAST (Fatigue, Aerodynamics, Structures, Turbulence) model provides
comprehensive aeroservoelastic capabilities.  

More information: https://nwtc.nrel.gov/SOWFA

.. admonition:: Download SOWFA

   This version of SOWFA is freely available on GitHub: `SOWFA <https://github.com/a2e-mmc/SOWFA-models>`_



Exawind
===========

AMR-Wind
--------
.. todo::
  **Fill in this section.**

More information: https://amr-wind.readthedocs.io


