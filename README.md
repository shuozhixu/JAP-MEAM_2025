# Non-dilute random alloys: Modified embedded-atom method potential

## Foreword

In this repository, we will study the effect of the chemical short-range order (CSRO) on lattice parameter and unstable stacking fault energy (USFE) in eight equal-molar body-centered cubic (BCC) refractory non-dilute random alloys: NbTa, NbTi, NbV, NbTaTi, NbTaV, NbTiV, NbTaTiV, and NbTaTiVZr.

[Another GitHub repository](https://github.com/shuozhixu/CMS-EAM_2025) answered two questions while using the embedded-atom method (EAM) potential. This repository aims to assess if those answers depend on the interatomic potential and/or the number of constituent elements. Specifically, we will employ [a modified embedded-atom method (MEAM) potential](https://www.ctcms.nist.gov/potentials/entry/2024--Nitol-M-S-Echeverria-M-J-Dang-K-et-al--V-Nb-Ta-Ti-Zr/2024--Nitol-M-S--V-Nb-Ta-Ti-Zr--LAMMPS--ipr1.html) developed by [Nitol et al.](https://doi.org/10.1016/j.commatsci.2024.112886)

A relatively minor issue we will address here is whether the two USFEs respectively calculated in energy minimized structure and non energy minimized structure are the same for the same composition and ordering. In two related GitHub repositories, one for [EAM](https://github.com/shuozhixu/CMS-EAM_2025) and another for [MTP](https://github.com/shuozhixu/CMS-MTP_2025), the USFEs for random alloys were calculated using either energy minimized or non energy minimized structures, depending on the data source. Here, we will compare them using the random structures.

Note that all CSRO structures are energy minimized.

## LAMMPS

LAMMPS on [OSCER](http://www.ou.edu/oscer.html) likely does not come with many packages. To finish tasks in this repository, at least three packages are needed.

- MANYBODY package. This is to use the manybody potential such as the EAM potential.
- MC package. This is to generate materials with CSRO at a given temperature. [This paper](http://dx.doi.org/10.1103/PhysRevB.85.184203) and [this paper](https://doi.org/10.1103/PhysRevB.86.134204) should be cited if one uses this package.
- MEAM package. This is to use the MEAM potential.

To install LAMMPS with these three packages, use the file `lmp_mbmcmeam.sh` in the `meam/` directory in this GitHub repository. First, cd to any directory on OSCER, e.g., \$HOME, then

	sh lmp_mbmcmeam.sh

Note that the second command in `lmp_mbmcmeam.sh` will load a module. If one cannot load it, try `module purge` first.

Once the `sh` run is finished, we will find a file lmp_mpi in the `lammps_mbmcmeam/src` directory on OSCER. And that is the LAMMPS executable with MANYBODY, MC, and MEAM packages.

## Random and CSRO structures

Random and CSRO structures of the eight alloys, generated by the NPT method, are in the `structure/` directory in this GitHub repository. If the last three letters of a filename is `NEM`, that structure is non energy minimized.

## Lattice parameter

Based on those structures, the lattice parameters of all 16 structures can be calculated by

	(lx/(10*sqrt(6.))+ly/(46*sqrt(3.)/2.)+lz/(14*sqrt(2.)))/3.
	
where

	lx = xhi - xlo
	ly = yhi - ylo
	lz = zhi - zlo

where `xhi`, `xlo`, `yhi`, `ylo`, `zhi`, and `zlo` can be found in the first few lines of the data file.

Let's denote the lattice parameter as $a_0$.

Note: for the same random alloy, e.g., random NbTa, use either `data.NbTa_random` or `data.NbTa_random_NEM` because they have the same lattice parameter.

## USFE

In what follows, let's use the energy minimized random quinary NbTaTiVZr as an example. The simulation requires files `data.NbTaTiVZr_random`, `lmp_gsfe.in`, `VNbTaTiZr.library`, `VNbTaTiZr.parameter`. The second file and the last two files are in the `gsfe/` and `meam/` directories, respectively, in this GitHub repository.

### Plane 1

Modify `lmp_gsfe.in`:

- line 18, replace the number `3.3` with $a_0$

Then run the simulation. Once it is finished, we will find a new file `gsfe_ori`. Run

	sh gsfe_curve.sh
	
which would yield a new file `gsfe`. The first column is the displacement along the direction while the second column is the generalized stacking fault energy (GSFE) value, in units of mJ/m<sup>2</sup>. The USFE is the peak GSFE value.

### Other planes

To calculate the GSFE curves on other planes, we make one more change to `lmp_gsfe.in`:

- line 48, replace the number `1` with `2`

The run the simulation and obtain another USFE value.

We can then replace that number with `3`, `4`, `5`, ..., `20`, respectively. It follows that we run 18 more simulations. Eventually, we obtain 20 USFE values in total. We then calculate the mean USFE value for the random quinary.

### Non energy minimized system

Use the data file `data.NbTaTiVZr_random_NEM` and make one change to `lmp_gsfe.in`:

- line 16, use the correct data file, `data.NbTaTiVZr_random_NEM`

### Other alloys

For another alloy, e.g., CSRO NbTaTi, make three changes to `lmp_gsfe.in`:

- line 16, use the correct data file, `data.NbTaTi_CSRO`
- line 18, use the correct lattice parameter
- line 22, change the element list after (not before) `VNbTaTiZr.parameter` to `Nb Ta Ti`

## Reference

If you use any files from this GitHub repository, please cite

- Shuozhi Xu, Wu-Rong Jian, Irene J. Beyerlein, [Ideal simple shear strengths of two HfNbTaTi-based quinary refractory multi-principal element alloys](http://dx.doi.org/10.1063/5.0116898), APL Mater. 10 (2022) 111107
