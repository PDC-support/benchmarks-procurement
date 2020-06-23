# VASP benchmarks 

The VASP benchmark contains one test case which must be run with VASP 
version 6.1.0. All the input files to run the case can be found in the 
subdirectory [GaAsBi512](GaAsBi512/README.md).

## Source code

### Obtaining the source code

The version must be VASP 6.1.0. The software is not included with
this benchmark package archive. Getting this software is the
responsibility of the Tenderer. PDC does not have the redistribution
rights of this software. From the VASP download site
https://www.vasp.at/ the Tenderer will need to get two files:

1. vasp.6.1.0.tgz 
2. potpaw_PBE.54.tar.gz

### License

To register for a VASP license visit 
[https://www.vasp.at/vasp-portal/registration/](https://www.vasp.at/vasp-portal/registration/).

## Installation guide

Good starting points are the provided examples of "makefile.include"
in `/arch`. For details on how to compile, see:
http://cms.mpi.univie.ac.at/wiki/index.php/Installing_VASP The basic
procedure on how to compile the "vasp_std" binary for CPU test:

```bash
$ tar xzf vasp.6.1.0.tgz
$ cd vasp.6.1.0
```

copy suitable template for "makefile.include", for example:

```bash
$ cp arch/makefile.include.linux_intel makefile.include
```

edit "makefile.include" and compile VASP general binary

```bash
$ make std
```

which results in the binary `bin/vasp_std`.

## Performance considerations

VASP is in many cases memory-bound and also relies heavily on 
all-to-all MPI collectives in 3D FFT operations.

## Benchmark case

Running VASP is done in a separate directory `GaAsBi512/`. 
VASP is an MPI-parallel application launched in an MPI-standard way.

### Running benchmarks

There are 4 files needed to run VASP, all of which should be in the
same work folder. The following 3 input files are provided by PDC for
the test case in `GaAsBi512/`:
- INCAR - input parameters 
- KPOINTS - k-pointmesh
- POSCAR - crystal structure

In addition a file POTCAR is needed. It is constructed from potential
files available with the VASP license.  On the VASP download site;
find (under Potentials > PBE), download and extract
potpaw_PBE.54.tar.gz The respective potentials are then put in order
into the POTCAR file

```bash
$ cat potpaw_PBE.54/Ga/POTCAR > POTCAR
$ cat potpaw_PBE.54/As/POTCAR >> POTCAR
$ cat potpaw_PBE.54/Bi_d/POTCAR >> POTCAR
```

Observe that these exact potentials must be in this correct
order. (Their order reflects the set up of the atoms in the crystal
structure in POSCAR) A check with grep should now produce:

```bash
$ grep TITEL POTCAR
   TITEL  = PAW_PBE Ga 08Apr2002
   TITEL  = PAW_PBE As 22Sep2009
   TITEL  = PAW_PBE Bi_d 06Sep2000
```

Observe that the Bi_d and not the Bi POTCAR must be used.


### Allowed changes to input files

POSCAR, POTCAR and KPOINTS files must not be changed. 
In INCAR you are allowed to adjust the following settings:

1. NSIM  
   Typically values should be > 1, important to check larger values for optimal performance. 
   The VASP default 4 is usually fine.
2. NCORE  
   Note, in this case you must set a value. Typically, the number of cores used per node. 
3. KPAR  
   It is possible to use k-point parallelization. Note that it can give a significant 
   speed-up in some cases. Since this test has 4 k-points, the choices are to set 
   KPAR = 2, 4 or comment it out for no k-point parallelization. 



### Validation of results

After finishing a run go through the following steps:

- Make sure that the NBANDS parameter is equal to (or larger than): NBANDS = 1536. 
  Check with: `$ grep 'NBANDS=' OUTCAR`

Confirm that the calculation has performed 13 iterations and that the
total energy has converged by inspecting the OSZICAR output file (the
energy changes much in the beginning):

- Check that column N goes to 13
- Check that the value of F= in last line agrees within `-2110.32+/-0.01`

### Minimum performance

This VASP benchmark will be evaluated according to how many jobs can
be performed on the total system in one day, according to the
throughput formula.

We require a *minimum performance*: the average runtime must not be
larger than 300 seconds (i.e. "real time" must not be larger than
300 s).

### Obtaining benchmark results

The execution time should be extracted from the OUTCAR file:  
`$ grep LOOP+ OUTCAR`  
The correct value is right after "real time".

This benchmark will be evaluated by the throughput metric, i.e. how
many jobs can be completed on the tendered system in one day. 
This number should represent 
an average and does not need to be an integer.
To calculate the number of jobs per day the throughput formula 
must be used:  
`n_{jobs} = t_{day} / t * N_{tot} / N`

where
- `t_{day}` = time in a day (86400 s)
- `t` = average time to run one job for this benchmark
- `N_{tot}` = total number of nodes tendered
- `N` = number of nodes used to run this benchmark
- `n_{jobs}` = number of jobs that can be run in a day for this benchmark 

`n_{jobs}` must be entered into the benchmark results spreadsheet.


## Contact

Questions regarding the benchmark(s) must be posted via the "question and answer function" in Visma TendSign.
