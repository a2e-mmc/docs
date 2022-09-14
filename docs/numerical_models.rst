****************
Numerical Models
****************


Weather Research and Forecasting (WRF) Model
============================================
WRF is a tool for numerical weather prediction (NWP) developed at the National
Center for Atmospheric Research (NCAR), the National Oceanic and Atmospheric
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

Community Survey
----------------
We conducted a survey to better understand how WRF is being used for
wind-energy applications. More specifically, the goal of the survey was to
provide guidance on best practices, identify aspects of model usage in need of
further investigation, and gain insight into barriers experienced by
wind-energy modeling teams in the use of WRF for their research.

For survey questions, results, and discussion including research opportunities,
see:

Colleen Kaul et al. WRF Modeling Practices for Wind Energy: Current Status and Future Needs. Technical Report PNNL-xxxxx, Pacific Northwest National Laboratory, 2022.


Simulator fOr Wind Farm Applications (SOWFA)
============================================
SOWFA is a set of CFD tools developed at the National Renewable Energy Laboratory (NREL) based on
the open-source OpenFOAM (Field Operation And Manipulation) platform for
simulating the microscale atmospheric boundary layer environment. Wind turbines
are modeled as actuator lines (or actuator disks) with the option to include
momentum sources to represent the wind turbine tower and nacelle. Coupling to
the NREL OpenFAST (Fatigue, Aerodynamics, Structures, Turbulence) model provides
comprehensive aeroservoelastic capabilities.  

More information: https://nwtc.nrel.gov/SOWFA

.. admonition:: Download SOWFA

   SOWFA is freely available on GitHub: `SOWFA-6 <https://github.com/NREL/SOWFA-6/tree/dev>`_. SOWFA-6 depends on `OpenFOAM-6 <https://github.com/OpenFOAM/OpenFOAM-6>`_. To use wind turbine aeroelastic coupling, `OpenFAST <https://github.com/OpenFAST/openfast>`_ is required.  


AMR-Wind
========
AMR-Wind is a massively parallel, block-structured incompressible LES for wind energy applications.
Based on the AMReX framework, it is well equipped to run on next-generation, leadership-class
supercomputers with CPU and/or GPU architectures. The software is under active development by
researchers at NREL, Sandia National Laboratories, and Lawrence Berkley National Laboratory. Like
SOWFA, AMR-Wind has the ability to perform profile assimilation and interface with `OpenFAST
<https://github.com/OpenFAST/openfast>`_ for aeroelastic modeling. It can also be coupled with
`Nalu-Wind <https://github.com/exawind/nalu-wind>`_, an overset near-body flow solver, amd behave as
a background solver.

More information: https://exawind.github.io/amr-wind/

.. admonition:: Download AMR-Wind

   AMR-Wind is freely available on GitHub: `amr-wind <https://github.com/exawind/amr-wind>`_.

