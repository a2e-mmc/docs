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


Other Codes
===========

Nalu
----
An exascale-capable microscale code under current development by NREL and Sandia
National Laboratories using the Sierra Toolkit and Trilinos solver. MMC
capabilities from SOWFA will be extended to Nalu in the future.

More information: https://nalu.readthedocs.io


