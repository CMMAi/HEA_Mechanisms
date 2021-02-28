# HEA mechanisms
In this repositiory, we demonstrate the LAMMPS code used in our MD simulations and the snapshot videos for our simulations. An example of an equimolar CoCrFeMnNi HEA system is demoed in this repository. You can generate your preferable input and data files using the code from this repository. 

You can find our simulation code in the src folder. The MEAM potential files were work published by Choi et al. 2018 [[1]](#1).

## Tensile simulation
If you wish to run our code, we use the **`lammps-16Mar18`** version of LAMMPS with mpi execution.
The **`equimolar.data`** file is our data file, you can also generate your preferable atom configuration. Please note that the simulation box size should be identical and the lattice parameter is 3.595 Angstroms. Our data is in [100], [010], [001] axial directions.

The **`tensile_equimolar.in`** is our input file. The simulation process is as follows:
1. Energy minimization
2. Lattice relaxation
3. Monte Carlo simulation
4. Quenching process
5. Boundary condition readjustment
6. Tensile simulation

#### Energy minimization
We use min_style `sd` to initialize the model and `dnax` value of 0.2. This is because we only want to ensure the stability of the model, and the precision would not matter much in this case.

The atomic stress is also defined in this section.

#### Lattice relaxation
Since our boundary condition is currently periodic, we here run `isothermal–isobaric ensemble` (NPT) for initial lattice relaxation. This is also the stage we assign velocity to the system, so a random seed is needed for this step. In our simulations, we changed the random seed in the same composition for maximizing our simulation validity (we usually run 4 or 5 times within one composition, using the same data dile and different random seed). We chose a 1.8 darg for the barostat/thermostat, but you can tune to your preference value.

#### Monte Carlo simulation
The `Monte Carlo` simulation is ultilized here is because we want to simulate the short-range ordering effect and lattice distortion effect of the HEA systems. By minimizing the system energy using Monte Carlo method, we wish the configuration of the system can be more realistic. The simulation steps for swapping is 15000. So if you are performing a composition with one of the elements being set to 0 molar ratio, the swapping steps should be annotated.

We experimented swapping multiple atoms within one step, but the result in convergent and the major difference is the simulation time. Also, the swapping steps is not fine tuned so there may be an optimum number for a given atom number. We only chose a reasonable number of steps because it is time consuming.

![](https://github.com/CMMAI-KTChen/Defect-evolution-of-HEA/blob/master/pic/MonteCarlo.gif)

#### Quenching process
Again, this part of the code aimed for more realistic local configuration. We heat the system from 300K to 1500K, equilibrium under 1500K, then cool back to 300K, follows by a equilibrium under 300K. `NPT` ensemble is used in this section. In our simulations, 1500K is high enough for the system to melt and rearrange. However, there might be a chance that the final lattice structure remained amorphous, such as Ni0. Defects like stacking faults, intrinsic stacking faults and extrinsic stacking faults would often emerge in this part of the simulation.

![](https://github.com/CMMAI-KTChen/Defect-evolution-of-HEA/blob/master/pic/quenching.gif)

#### Boundary condition readjustment
In this simulation, we are interested in the ductile property of the system. So after pormising initial configuration is formed, we switched the boundary condition to non-periodic so that tensile simulation could be prolonged and deformation mechanisms during the tensile process could be observed. After switching to non-periodic boundary condition, we also switched to `Canonical ensemble` (NVT) and ran a relaxation to relax the surface stress. In most cases, the initial stress could be reduced to around -2 order of GPa.

![](https://github.com/CMMAI-KTChen/Defect-evolution-of-HEA/blob/master/pic/lattice_relaxation.gif)

#### Tensile simulation
In the tensile simulation, we fix the upper and lower 27 angstroms of the model and give them fixed velocity `0.3 Angstom/picosecond` (0.15 upwards and -0.15 downwards). Strain and stress were calculated using the region where the fixed regions were excluded.

## Simulation snapshots
Animation of all our simulation runs are shown in this project. You can find animations processed using **Common Neighbor Analysis (CNA)** and our self-developed **Planar Defect Identification (PDI)** algorithm via OVITO. In CNA processed videos, atoms with fcc crystal structure is colored green, bcc crystal structure is colored blue and hcp crystal structure is colored red. Amorphous atoms are excluded in all videos. Each animation is recorded till the model rupture or an amorphous necking formed.

![image](https://github.com/CMMAI-KTChen/Defect-evolution-of-HEA/blob/master/pic/legends_PDI.png)

If you have any question about our project, please contact our email address: dchen@ntu.edu.tw

## References
<a id="1">[1]</a> 
W.-M. Choi, Y.H. Jo, S.S. Sohn, S. Lee, and B.-J. Lee (2018), "Understanding the physical metallurgy of the CoCrFeMnNi high-entropy alloy: an atomistic simulation study", npj Computational Materials, 4(1), . DOI: 10.1038/s41524-017-0060-9.
