******************
Code Contributions
******************

Inflow Perturbation Methods
===========================

Several inflow perturbation methods were developed and examined within the Mesoscale-to-Microscale
Coupling (MMC) project. The goal of these methods is to accelerate the formation and equilibration
of resolved-scale turbulent flow features within large-eddy simulation (LES) domains driven by
forcing or inflow that does not contain resolved-scale turbulence features explicitly. Inflow
perturbations can significantly reduce the distance required for turbulence features to form and
equilibrate to the extant forcing, leading to substantial reductions of computational costs by
permitting the use of smaller domains, while simultaneously improving the physical accuracy of the
solution in the area of interest.

Cell Perturbation Method 
------------------------

This section describes our implementation of the Cell Perturbation Method (CPM) into the MMC version
of the the Weather Research and Forecasting (WRF) model. This implementation encompasses several
variants examined within the references, as well as some additional configurability, along with a
few differences. Additional information about this specific implementation can be obtained from two
files within the MMC WRF installation; **Registry/registry.les_cpm**, which describes the model
variables, runtime configurability, units, and default values; and **dyn_em/module_les_cpm.F**,
which contains the source code as well as descriptions of functionality in the header of each
subroutine, along with descriptions and units for all variables. While this document describes our
implementation into the WRF model, the same general algorithm can be applied to other LES models
that also receive inflow information either from a mesoscale atmospheric simulation, or
slow-response measurements that likewise do not contain resolved-scale turbulence motions. 

The CPM is a technique based on the application of perturbed values of state variables (temperature
or velocity) to one or more "cells" (groups of one or more contiguous model grid points in the
horizontal and vertical directions) located along one or more lateral boundaries of a computational
domain. Optimal choices of the size, amplitude, and number of cells effectively seeds the inflow to
an LES domain with resolved-scale variability at the proper scales and magnitudes to rapidly
generate resolved-scale turbulence that is consistent with the large-scale forcing. The value of the
perturbations are constant within each cell, and vary randomly from cell to cell within a target
amplitude window.  


:numref:`fig-cpm_1` depicts a typical application of the CPM, following the approaches developed and
used in many of the references. Here, perturbations (colored squares) are added instantaneously to
the potential temperature field, around all four lateral edges of an LES domain, nested one-way
within a bounding mesoscale domain (not shown), from which it receives lateral boundary information.
The LES domains shown in this section are much smaller than would typically be used, with the
purpose here to elucidate functionality and options. A sample subset of the namelist.input files
that controls CPM characteristics is shown to the left of :numref:`fig-cpm_1` and those to follow.
Our implementation provides default values for many of these control parameters, taken from the
literature, but also provides flexibility to modify many of these parameters at run time, to
facilitate optimization for various applications. These and other configuration parameters are
described and exemplified in the remainder of this section.

.. _fig-cpm_1:
.. figure:: img/CPM_OPT_1_N8_C3_V1_B_ALL_AMP1_DT1_CPM_XY_namelist.png
  :width: 800
  :align: center

  Schematic of the CPM applied to potential temperature, with the standard 8X8 horizontal cell size, with 3 cells, around all four lateral boundaries, and a target amplitude window of [-1,1] K. 

The first line of text shows that the CPM is not used in the bounding domain (``cpm_opt`` = 0 in
column one, corresponding to the outermost WRF domain), and is used in the LES domain nested within
it (``cpm_opt`` = 1 in column two). Values of ``cpm_opt`` = 1 - 5 enable differences in the
formulations and configurability of the CPM, which will be described further below.

The next two namelist entries ``cpm_meso_pblh`` and ``cpm_lim_z`` determine the height above the
surface to which perturbations are applied. If ``cpm_meso_pblh`` = 0, ``cpm_lim_z`` determines the
maximum height up to which to apply the perturbations. Setting ``cpm_meso_pblh`` = 1 instead uses
the planetary boundary layer (PBL) height predicted by one of WRF's mesoscale PBL schemes. This
option requires that ``cpm_meso_pblh`` = 1 on any domain using the CPM, as well as at least one
bounding domain within which a mesoscale PBL scheme is used. In both case, the perturbations will be
applied from the vertical index ``cpm_noff_z`` above the surface, up to and including the highest
model grid level below a height equal to either 75% of the mesoscale PBL height for
``cpm_meso_pblh`` = 1, or to ``cpm_lim_z`` for ``cpm_meso_pblh`` = 0. Both the mesoscale PBL height
and the height of the model vertical index used in these calculation are obtained using averages of
all four lateral edges within each domain using the CPM. The slightly reduced height relative to the
mesoscale PBL height prevents the triggering of anomalously strong mixing near the PBL top. When
``cpm_meso_pblh`` = 1, parameter ``cpm_lim_z`` instead specifies a minimum value to apply the
perturbations, in the event that the mesoscale PBL scheme diagnoses a very shallow PBL, as sometimes
occurs during stable conditions. 

The next four parameters ``cpm_nb``, ``cpm_sb``, ``cpm_wb`` and ``cpm_eb`` specify which among the
the north, south, west and east boundaries, respectively, to apply the perturbations along, selected
with a value of 1. Alternatively, if these parameters are all set to 0 (their default values), the
boundaries to perturb will instead be selected automatically based on the lateral edge-averaged
horizontal velocity components at the vertical grid index just below the height at which the wind is
assumed to be approximately geostrophic. This height is taken to be 125% of either ``cpm_lim_z`` or
the lateral edge average of ``cpm_meso_pblh``. Perturbations are then applied to each lateral edge
for which the flow is oriented into the domain. This option allows the edges being perturbed to
change automatically over time with changes of the large-scale wind direction.

The next parameter ``cpm_amp`` specifies the target perturbation amplitude window. This value can be
determined from among four options. Setting ``cpm_amp`` > 0.0 uses that value as the target, with
each cell's value drawn from a uniform random number distribution, shifted to a zero mean, and
scaled such that the range of values spans [-``cpm_amp``, ``cpm_amp``]. If ``cpm_amp`` = 0.0 (the
default value), the amplitude is instead determined automatically using one of the formulations
specified by ``cpm_opt`` = 2-4, as described below.


:numref:`fig-cpm_2`, :numref:`fig-cpm_3`, and :numref:`fig-cpm_4` demonstrate some of the
above-discussed configurability. For example, in :numref:`fig-cpm_2` the perturbation zone contains
smaller cells and more rows, while in :numref:`fig-cpm_3` and :numref:`fig-cpm_4` a smaller number
of larger cells is specified, along with different boundaries and amplitudes. Note that in
:numref:`fig-cpm_1` and :numref:`fig-cpm_2`, the total numbers of grid points defining the domain
extent, here [nx,ny] = [288,144] in the x- and y-directions, respectively, are divisible by
``cpm_ngc_h``, with no remainder, resulting in cells of the same size along all boundaries. In
contrast, :numref:`fig-cpm_3` and :numref:`fig-cpm_4` use values of ``cpm_ngc_h`` that do not divide
evenly into ni or nj, leaving portions of cells equal to the remainder of ni or nj divided by
``cpm_ngc_h``. In this implementation, the smaller cells occur at the north and east edges of the
domains, since the tilings of the north and south boundaries begin at the west boundary, while those
of the west and east boundaries begin at the south boundary. The order of the boundary tiling is
west, east, south, then north, which results in the fractional cells appearing along different
tilings at the east boundary edges in :numref:`fig-cpm_3` and :numref:`fig-cpm_4`. One way to
eliminate fractional cells is to ensure that ``cpm_ngc_h`` divides into ni and nj with no remainder.

.. _fig-cpm_2:
.. figure:: img/CPM_OPT_1_N4_C8_V1_B_NW_AMP1.5_DT1_CPM_XY_namelist.png
  :width: 800
  :align: center

  CPM namelist parameters specifying a larger number of smaller cells, different boundaries, and a larger amplitude.

.. _fig-cpm_3:
.. figure:: img/CPM_OPT_1_N14_C2_V1_B_SWE_AMP1_DT1_CPM_XY_namelist.png
  :width: 800
  :align: center

  CPM namelist parameters specifying a smaller number of larger cells, and different boundaries. 

.. _fig-cpm_4:
.. figure:: img/CPM_OPT_1_N14_C2_V1_B_NWE_AMP1_DT1_CPM_XY_namelist.png
  :width: 800
  :align: center

  CPM namelist parameters specifying the same tiling as for :numref:`fig-cpm_3`, but switching north and south boundaries. 

:numref:`fig-cpm_5` demonstrates three additional run-time options. One option is the automatic
selection of which boundaries to perturb, chosen by setting ``cpm_nb`` = ``cpm_sb`` = ``cpm_eb`` =
``cpm_wb`` = 0. In this example featuring southwesterly flow, the south and west boundaries are
selected. Another option shown here is how to stop the perturbations at a specified a number of grid
points adjacent to any outflow boundary using cpm_off_h. This offset will be applied at any boundary
that does not have perturbations applied, that is adjacent to a boundary that does. For
demonstration, in :numref:`fig-cpm_5`, ``cpm_noff_h`` = 5, which leaves cells of width 3 grid points
remaining along the north and east boundaries, since ``cpm_noff_h`` does not divide evenly into
``cpm_ngc_h``. Such smaller cells can be eliminated by specifying compatible values of
``cpm_noff_h``, ``cpm_ngc_h``, ni and nj.

:numref:`fig-cpm_5` also demonstrates one option for automatic determination of the perturbation
amplitude, ``cpm_opt`` = 2. This option bases the amplitude on the turbulent Eckert number,
following :cite:t:`Munoz-Esparza2014,Munoz-Esparza2015`. This formulation requires the magnitude of
the geostrophic wind, the value of which is determined using the lateral edge-averaged horizontal
wind components at the vertical grid cell index just below the height of the geostrophic wind, as
used for the automated boundary selection option. The case shown in :numref:`fig-cpm_5` used a
geostrophic wind speed of 15 m/s, resulting in a target amplitude of approximately 1.1 K.

.. _fig-cpm_5:
.. figure:: img/CPM_OPT_2_N8_C3_V1_B_AUTO_AMP_AUTO_DT_AUTO_CPM_XY_namelist.png
  :width: 800
  :align: center

  CPM namelist parameters specifying automated determination of which boundaries to perturb, and the target amplitude. 

The next parameter ``cpm_dt`` determines the time interval in seconds at which new perturbations are
applied. Specifying ``cpm_dt`` > 0.0 reapplies perturbations as soon as that time interval is
exceeded, based upon the model time step, to all grid cells within the perturbation zone,
``cpm_ngc_h`` * ``cpm_ncells_h``, at all lateral boundaries and at all heights to which the
perturbations are applied. :numref:`fig-cpm_6` demonstrates this option, with ``cpm_dt`` = 30.0,
showing x-z slices of potential temperature (top) and vertical velocity (bottom) after 5 minutes of
simulated time. In this example, which features significant wind shear (geostrophic wind speed = 15
m/s), the value of ``cpm_dt`` is chosen as a compromise between the advective timescales of the
perturbation zone near the surface versus farther aloft. For this strongly sheared case, the use of
one timescale for all heights results in an overlapping of the cells near the surface, which
compromises the performance of the method, while also yielding gaps in the potential temperature and
vertical velocity variability above. 

.. _fig-cpm_6:
.. figure:: img/CPM_OPT_2_N8_C3_V1_B_AUTO_AMP_AUTO_DT_30_SHEAR_CASE_PBL_T_W_XZ_namelist.png
  :width: 800
  :align: center

  Use of the specified perturbation refresh timescale leading to overlaps within and gaps between in the perturbations due to the vertical shear.

The overlapping within and gaps between the perturbations shown in :numref:`fig-cpm_6` can be
eliminated by setting ``cpm_dt`` = 0.0 (the default value), as shown in :numref:`fig-cpm_7`, which
uses a height-dependent time interval based upon the advective timescale of the perturbation zone at
the height of each slab. This timescale is computed as (1/a)*(``cpm_ngc_h`` * ``n_cells_h`` -
``cpm_lambda`` * ``spec_bdy_width``) * dh/ws. Here, dh is the model horizontal grid spacing, and ws
is the wind speed averaged over the depth of the slab, along all four lateral edges. The term 1/a
accounts for an increase of the advective timescale if the wind vector is not perpendicular to the
cell, with a maximum value of a = sqrt(2) for flow oriented along the diagonal, while the factor
cpm_lambda accounts for the effect of the relaxation zone, the number of grid cells adjacent to each
lateral boundary over which the nested domain solution is either set to or blended with the bounding
domain solution (``spec_bdy_width``). The default value of cpm_lambda = 0.5 assumes that the
perturbations are diminished sufficiently to be negligible over one half of ``spec_bdy_width``, and
shortens the advective timescale slightly to account for the slightly reduced width of the
perturbation zone. 

.. _fig-cpm_7:
.. figure:: img/CPM_OPT_2_N8_C3_V1_B_AUTO_AMP_AUTO_DT_AUTO_SHEAR_CASE_PBL_T_W_XZ_namelist.png
  :width: 800
  :align: center

  Use of an automated perturbation refresh timescale, which eliminates the overlaps within and gaps between the perturbations shown in :numref:`fig-cpm_5`. 

:numref:`fig-cpm_8` demonstrates changes to the thickness of the perturbation slabs, using a value of ``cpm_ngc_v`` = 2 grid points, versus ``cpm_ngc_v`` = 1 (the default value) used in :numref:`fig-cpm_7`. The use of thicker slabs leads to larger amplitudes of resolved-scale features for both potential temperature and vertical velocity.

.. _fig-cpm_8:
.. figure:: img/CPM_OPT_2_N8_C3_V2_B_AUTO_AMP_AUTO_DT_AUTO_SHEAR_CASE_PBL_T_W_XZ_namelist.png
  :width: 800
  :align: center

  As in :numref:`fig-cpm_7`, but using slabs with a depth of two grid points. 

Two other scaling formulations for the perturbation amplitude have been developed, following
:cite:t:`Munoz-Esparza2018`. :numref:`fig-cpm_9` demonstrates a method intended for stable
conditions, ``cpm_opt`` = 3, showing instantaneous X-Z slices of perturbation amplitude (top) and
vertical velocity (bottom). This method uses a modified Richardson number, here shown with the
default value of ``cpm_rim`` = -1.0. 

While difficult to discern, :numref:`fig-cpm_9` shows a slight increase in the magnitude of the
perturbations with height, above the decrease occurring over the lowest ten or so grid points. The
vertical velocity shows a corresponding local maximum of variability near the height of the top of
the perturbation zone. These features are due to the use of a stretched vertical grid in this setup,
which increases the vertical grid spacing with height, rather than using a constant vertical grid
depth as in the reference. The bulk CPM amplitude formulation uses vertical differences of wind
speed and potential temperature, rather than gradients. Therefore, all else being equal (e.g. for
constant vertical gradients), the ``cpm_opt`` = 3 formulation will compute larger vertical
differences, hence larger perturbation amplitudes, with height as the grid cell depth increases.
Therefore it is recommended to use constant vertical grid spacing with this option. 

.. _fig-cpm_9:
.. figure:: img/CPM_OPT_3_N8_C3_V1_B_AUTO_AMP_AUTO_DT_AUTO_SHEAR_CASE_PBL_DX6_CPM_W_XZ_namelist.png
  :width: 800
  :align: center

  Thermal perturbation magnitude (top) and vertical velocity (bottom) using a formulation for stable conditions.  

:numref:`fig-cpm_10` demonstrates a method intended for unstable conditions, ``cpm_opt`` = 4. This
method requires specification of the surface sensible heat flux, which can either be set within the
namelist using spec_hfx (shown here), or can use a value obtained from a mesoscale surface layer
scheme, by selecting ``cpm_meso_hfx`` = 1. As with ``cpm_meso_pblh``, the latter option requires
that ``cpm_meso_hfx`` = 1 on at least one domain using a mesoscale surface layer scheme, as well as
any LES domain using ``cpm_opt`` = 4.

.. _fig-cpm_10:
.. figure:: img/CPM_OPT_4_N8_C3_V1_B_AUTO_AMP_AUTO_DT_AUTO_SHEAR_CASE_PBL_CPM_W_XZ_namelist.png
  :width: 800
  :align: center

  Thermal perturbation magnitude (top) and vertical velocity (bottom) using a formulation for unstable conditions.  

The rationale for perturbing the potential temperature is to generate buoyancy accelerations that
will rapidly form robust turbulence structures with consistent velocity correlations. However,
``cpm_opt`` = 5 allows the user to instead perturb the vertical velocity field directly.
:numref:`fig-cpm_11` and :numref:`fig-cpm_12` demonstrate this option, using slabs with depths of
one and two grid points, respectively. As no established scaling for the amplitude of the vertical
velocity perturbations has been developed, the value of ``cpm_amp`` = 3.5 m/s was chosen to
reproduce a similar range of resolved-scale vertical velocity fluctuations as those produced using
the Eckert number formulation shown in :numref:`fig-cpm_6` - :numref:`fig-cpm_8`.

.. _fig-cpm_11:
.. figure:: img/CPM_OPT_5_N8_C3_V1_B_AUTO_AMP_3.5_DT_AUTO_SHEAR_CASE_PBL_W_XZ_namelist.png
  :width: 800
  :align: center

  Perturbing vertical velocity directly with a target amplitude window of 3.5 m/s, using slabs with a depth of one grid point. 


.. _fig-cpm_12:
.. figure:: img/CPM_OPT_5_N8_C3_V2_B_AUTO_AMP_3.5_DT_AUTO_SHEAR_CASE_PBL_W_XZ_namelist.png
  :width: 800
  :align: center

  Same as :numref:`fig-cpm_11`, but using slabs with a depth of two grid points. 


Discussion
^^^^^^^^^^
The implementation of the CPM described herein contains multiple options for
idealized and real conditions, much of which has been tested and published. However, significant
additional flexibility has been included, most of which remains untested. For example, the
recommended cell sizes and number of rows were established using the Eckert number formulation of
:cite:t:`Munoz-Esparza2014,Munoz-Esparza2015`, with a cell depth of one grid point. While the use of
slabs of a depth of two grid points may yield slightly more rapid development of turbulence motions,
the impact on the evolving turbulence field has not been established. The vertical velocity
application implemented herein also differs from the approach of :cite:t:`Mazzaro2019`, here
applying the perturbations directly to the vertical velocity field, rather than through the forcing
tendency as in the original formulation.

The expanded functionality of the MMC implementation of the CPM is intended to support further
optimization of the approaches, including further examination of the physical dimensions of the
cells, number of cells, amplitudes and refresh timescales, modified Richardson number, and other
parameters, for given sets of forcing and landscape conditions. However, until the performance of
any alternate configurations is established, it is recommended to use the default configuration
settings from the publications cited.


Mann Method
-----------
The synthetic perturbation method applies the turbulence field along the inflow boundaries of the LES domain. The turbulence can be generated using any stochastic method, such as Mann :cite:p:`Mann1998`, TurbSim :cite:p:`turbsim`, etc. Herein, we describe the implementation of Mann synthetic turbulence in the WRF model to spin up the turbulence. The turbulence field is prepared separately from the WRF model using the empirical relation for the synthetic turbulence method. A homogeneous turbulent field (with a mean value of zero) is applied along the inflow boundary of the microscale domain at the prescribed frequency (e.g., 10 sec). This will instigate turbulence in the incoming wind as it progresses downstream from the boundaries.

The amount of turbulence in the Mann method is controlled by the length scale and the spectral energy scaling intensity as the input parameters. If observational data are available, the intensity of the turbulence can be adjusted by scaling the square root of the variances calculated from the observations. The turbulence should not be added above the boundary-layer depth. The vertical inflow plane added to the wind field at the microscale boundary is shown in :numref:`fig-mann_turb` for two-length scales and turbulence scaling intensities. The first and second panels are the u-velocity field created with a length scale of 100 m and 200 m with the identical scaling intensity factor of 50%. The third panel shows the u-velocity same as the second, but the scaling intensity factor is reduced to 10%. The amount of turbulence in the simulated field in the microscale domain depends on these inputs.

.. _fig-mann_turb:
.. figure:: img/Mann_inflow.png
  :width: 800
  :align: center

  Inflow plane generated by the Mann method using length scale and scaling intensity of 100 m and 50% (left), 200 m and 50% (middle), and 200 m and 10% (right) 


TurbSim Method
--------------
TurbSim applies the Veers method :cite:p:`Veers1988` to generate time-varying homogeneous turbulent
inflow planes that are periodic in time. Inputs include either a modeled turbulence spectrum (e.g.,
the Kaimal model) or an input turbulent time history from which spectral amplitudes and phases can
be calculated. A tunable coherence model correlates the fluctuations of a particular velocity
component, between any two points on the inflow plane, as a function of their spatial separation.
Other parameters describing the synthetic turbulence include turbulence, integral-length, and
coherence scale parameters.

To account for nonstationarity and vertical heterogeneity, the simulated velocity fluctuations can
be scaled over time to match a known time history. Similarly, if information about the structure of
the ABL is known, the simulated velocity fluctuations can be scaled over height such that they are 0
in the free atmosphere. This information may be approximated by WRF PBL scheme outputs (``PBLH`` and
``TKE_PBL``) if available to produce inflow planes such as those seen in :numref:`fig-turbsim_turb`.

.. _fig-turbsim_turb:
.. figure:: img/TurbSim_snapshots.png
  :width: 800
  :align: center

  Inflow planes during a morning transition generated by TurbSim, using time-varying velocity
  variance and ABL height scaling

.. admonition:: Example code

    Example code for extracting TurbSim fluctuations offline for use with SOWFA or WRF-LES can be
    found in the `GitHub repo
    <https://github.com/a2e-mmc/assessment/blob/master/studies/SWiFT/coupling_comparison/preprocessing/boundary/turbsim_to_velocity_perturbations.ipynb>`_.

An idealized verification study :cite:p:`Quon2018` showed that the addition of synthetic momentum
perturbations can reduce the fetch--the region over which turbulence develops on a finite
(aperiodic) domain--by a kilometer or more. The study used underresolved turbulence fields from a
coarse LES mesh (with 40 m spacing) as initial conditions and then compared the evolution of one-
and two-point statistics on a fine mesh (10 m), with and without perturbations. TurbSim and a
turbulence enrichment methodology (Gabor Kinematic Simulation) were both found to be similarly
effective in this study.


Three-Dimensional Planetary Boundary Layer (PBL) Parameterization
=================================================================

Parameterization description
----------------------------

Led by MMC team members Branko Kosović, Pedro Jimenez, and Timothy Juliano, a three-dimensional (3D)
planetary boundary layer (PBL) parameterization based on the Mellor-Yamada (MY) framework was
implemented into the Weather Research and Forecasting (WRF) model. The MY model is fundamentally
rooted in the Reynolds Averaged Navier-Stokes (RANS) equations. Motivation for the 3D PBL
parameterization stems from the assumption of horizontal homogeneity made by traditional 1D PBL
parameterizations. This fundamental assumption becomes unreasonable for high-resolution (horizontal
grid cell spacing, Δ≈100-1000m) mesoscale simulations due to the increasing importance of horizontal
variability in surface forcing. In this modeling regime, nicknamed the *Terra Incognita* by :cite:t:`Wyngaard2004` and sometimes referred to as the gray zone, turbulence is neither fully parameterized
(as in the mesoscale limit) nor fully resolved (large-eddy simulation, or LES, limit).

The main challenge that most 1D PBL parameterizations face in the gray zone is that the horizontal
homogeneity assumption ignores all horizontal gradients, in addition to the vertical gradient in
vertical velocity. As a result, only the vertical turbulent stresses and fluxes are parameterized,
and they are computed based on only the vertical gradients. However, at gray zone resolutions, the
ratio of horizontal to vertical shear may be as large as 0.2 :cite:p:`Kosovic2021`, suggesting
horizontal gradients should be considered when computing turbulent stresses and fluxes. Furthermore,
1D PBL schemes do not compute horizontal flux divergences, but rather horizontal diffusion is
introduced (typically using a 2D Smagorinsky-type approach) to maintain numerical stability
:cite:p:`Smagorinsky1993`. Under unstable conditions, spurious convective structures often manifest
due to the inability of 1D PBL schemes to accurately represent turbulence (e.g., Ching et al. 2014).
To address this gray zone modeling challenge, the 3D PBL scheme implemented by the MMC team does not
make the horizontal homogeneity assumption, and therefore it more accurately models turbulent PBL
flow.

The 3D PBL parameterization was implemented into the WRF model in a similar fashion as the turbulent
kinetic energy (TKE)-based LES subgrid-scale (SGS) model of :cite:t:`Deardorff1980`. Most of the 3D
PBL code is self-contained in two modules within WRF’s dynamics (``dyn_em`` directory):
``module_pbl3d.F`` and ``module_pbl3d_my.F``. The first module (``module_pbl3d.F``) acts as the
driver for the parameterization, while the second module (`module_pbl3d_my.F`) contains the MY
model. First, the driver computes the 3D gradients of the 3D wind components and scalar quantities
(potential temperature and water vapor mixing ratio; however, the model can be extended to other
scalars). Then, this information is passed into the MY model to compute all six turbulent stress
components, as well as the 3D turbulent fluxes for each scalar. All of the turbulent fluxes are
passed back into the driver, where the turbulent stress and flux divergences are computed and added
to the right-hand side of the respective prognostic equations. More details about the
parameterization methodology, in addition to various idealized and real cases, may be found in
:cite:t:`Kosovic2020,Juliano2022,Eghdami2022,Arthur2022,Rybchuk2021wesd`.


WRF namelist options and example
--------------------------------

Several new namelist options specific to the 3D PBL parameterization have been added to the WRF
model. These namelist options are detailed in run/README.namelist and reproduced here: 

.. _inputs_3DPBL:
.. figure:: img/3DPBL_namelist.png
  :width: 800
  :align: center

  WRF namelist.input options and descriptions for the 3D PBL parameterization

The following example namelist options are shown for three different 3D PBL configurations ran for
an idealized convective seabreeze case, as reported in :cite:t:`Juliano2022`.

 =================== ======== =========== ========= 
 &dynamics           "SMAG"   "3D-APPROX" "3D-FULL" 
 =================== ======== =========== ========= 
 ``pbl3d_opt``         -1          1          2     
 ``pbl3d_prog``         1          1          1     
 ``pbl3d_nsteps``       1          1          1     
 ``pbl3d_sfc_opt``      1          1          1     
 ``pbl3d_l_opt``        3          3          3     
 ``pbl3d_constants`` 'Boulac'   'Boulac'   'Boulac' 
 =================== ======== =========== ========= 

An example result from these seabreeze simulations is now shown (reproduced from
:cite:t:`Juliano2022`). Results from these three 3D PBL configurations are compared with a 20-member
LES ensemble because we expect that the 3D PBL results represent the ensemble mean (RANS) solution
(i.e., the turbulence should be fully parameterized and thus the resolved flow should be
nonturbulent according to :cite:t:`Wyngaard2004`). Further discussion may be found in
:cite:t:`Juliano2022`.

.. _3DPBL_slices:
.. figure:: img/3DPBL_slices.png
  :width: 800
  :align: center

  Horizontal slices of vertical velocity (w, units of m/s) at 100 m above sea level and t = 345 min
  for three different 3D PBL configurations (Δ = 250 m) of the seabreeze case. Figure is reproduced
  from :cite:t:`Juliano2022` (their Fig. 15).

The impact of the turbulence closure/mixing approach (controlled by namelist option ``pbl3d_opt``)
is evident in :numref:`inputs_3DPBL`: the “SMAG” and “3D-APPROX” setups – neither of which
account for horizontal gradients – produce spurious convective structures to the east of the sea
breeze front (manifested as the north-south oriented strip of positive vertical velocity at ~25 km).
Over this land region, the surface heating is horizontally homogeneous, and thus one would expect to
see no organized structures. However, in the “3D-FULL” setup, which does consider horizontal
gradients, spurious convective structures are not evident. The “3D-FULL” configuration is much
closer to the LES ensemble, which shows very little organized structure to the east of the sea
breeze front.  Therefore, this example illustrates the important role that horizontal gradients play
at gray zone resolutions and under convective conditions.


.. admonition:: Code Access

    The WRF model code that contains the `3D PBL parameterization
    <https://github.com/twjuliano/WRF/tree/develop_3dpbl_on_top>`_ is publicly available on GitHub.


References
==========

.. rubric:: Resulting Publications

.. bibliography:: all_project_pubs.bib
    :filter: (mmc_rtd_section % "perts") or (mmc_rtd_section % "dev")

.. rubric:: Other

.. bibliography:: code_refs.bib

