# NEK5000 benchmark

This directory contains source code, restart files and run files
for the Nek5000 benchmark. The directory structure is as follows:
- `ReTau1000/nek/`: source code and makefiles
- `ReTau1000/rs8_restart/`: restart files for the benchmark run
- `ReTau1000/pnpn/`: run directory with input files

Nek5000 ( http://Nek5000.mcs.anl.gov ) is an open-source code for the
simulation of incompressible flow. Nek5000 is widely used in a broad
range of applications, including the study of thermal hydraulics in
nuclear reactor cores, the modelling of ocean currents and the
simulation of combustion in mechanical engines. The Nek5000
discretization scheme is based on the spectral-element method. In this
approach, the incompressible Navier-Stokes equations are discretized
in space by using high-order, weighted residual techniques employing
tensor-product polynomial bases. The resultant linear systems are
computed using Generalized minimal residual method (GMRES) with
convenient preconditioners. The tensor-product-based operator
evaluation can be computed as matrix-matrix products.

The application consists of 100,000 lines of code and it is written
mainly in Fortran (70,000 lines of code) and in C (30,000 lines of
code). Nek5000 uses the Message Passing Interface (MPI) and domain
decomposition for parallel computing on distributed memory
supercomputers.

## Source code

### Obtaining the source code

The source code is located in the `nek/` subfolder together 
with makefiles `makenek` and `makenek.inc`.

### Guidelines for Porting and Modifying the Source Code

Modifications to the Nek5000 source code are acceptable as long as any
modifications are made available to the Nek5000 development team 
before acceptance benchmark runs on the delivered system.
All modifications must be general and not problem-specific.

Modifications of the makefiles are also permitted, i.e. the 
files `makenek` and `makenek.inc`.

The file `SIZE` contains parameters which define various dimensions 
for the particular benchmark case. Only two parameters can be adjusted:
- `lp`: upper limit for number of CPUs. Currently set to 8192 
  but needs to be increased to 100000 or more
- `lelt`: can be adjusted for memory and CPU efficiency. 
  Should be larger than nelt, the number of elements per MPI rank:
  nelt = lelg(number of elements)/np(number of processes) = 1264032/np .
  With the default setting (lelt=1250) the case can not be run on less
  than 1024 ranks since e.g. on 1000 ranks: 1264032 / 1000 = 1264 > 1250.
  
## Installation guide

The source code provided has been tested with Cray, Intel, and GNU
compilers. Two script files makenek and makenek.inc, which are used in
the compilation process, need to be adapted to the compilation
environment used.

Out-of-source compilation should be done in, e.g., the run directory
`pnpn/`. The build script `pnpn/makenek` must be adjusted and
compilation flags can be modified below line 196 in the file
`nek/makenek.inc`.  The benchmark case is compiled with the command
`makenek pipe`.

**Side note:** In order to compile with gcc v8.X, the flag "-std=legacy" needs to be added.

## Benchmark case

The test case (under subfolder [pnpn/](pnpn/)) 
considered is the turbulent flow in a straight pipe, a
generic and widely known case across the CFD community. The flow is
run with a friction Reynolds number of 1000 and 1,264,032 elements.
A strong scaling study of this case has been 
[published previously](https://dl.acm.org/doi/10.1145/2938615.2938617).

### Running the benchmark

The benchmark has a total memory requirement of around 7000 GB, and is
sufficiently large to potentially scale to a significant part of the
proposed machine. In general it is expected that the highest
performance will be obtained when the number of MPI ranks is a factor
of two, but it is permissible to run on core counts which are not
factors of two if it gives better performance. **Full nodes (all cores 
per node) must be used for the benchmark runs**.

Only one metric is used to evaluate the benchmark which is
proportional to the time to solution.  Vendors should run the
benchmark using increasing number of MPI ranks until no improvement in
performance is observed.

The benchmark will run for 50 steps (as specified in `pipe.rea`).

It is important to add the following lines to the job script:
```
casename=pipe
rm -f $casename.sch *_host_*
echo $casename > SESSION.NAME
echo $PWD/ >> SESSION.NAME
```

To run the nek5000 executable, the job script should contain
```
mpirun/srun/similar ./nek5000 > log.nek5000.$JOBID 2>&1
```

### Validation of results

It is important to check that results from Nek5000 are scientifically
valid. Vendors can check the results by executing `cat
<log.nek5000.file> | grep "PRES gmres"`, and checking that the second
integer in every line (number of iterations for the linear solver) is
less than 15 for the last 20 time steps.

### Obtaining benchmark results

Vendors must provide information from the Nek5000 stdout, which
can be redirected to a file. 
After running 50 steps, the simulation will print a line containing 
"overhead" which shows the total execution time for the last 20 steps.
To extract it, type:
`grep overhead <log.nek5000.file>`

This benchmark case will be evaluated by the strong-scaling metric,
i.e. how many jobs can be completed on the tendered system in one day
given that only one job runs at the time (one job has access to the
entire machine).  **One job for Nek5000 is considered to be the last
20 steps of the simulation**.  
To calculate the number of jobs per day
the strong-scaling formula must be used:   
`n_{jobs} = t_{day} / t`  
where: 
- t_{day} = time in a day (86400 s) 
- t = average time to run one job (last 20 timesteps) for this benchmark 
- n_{jobs} = number of jobs that can be run in one day for this benchmark

`n_{jobs}` must be entered into the benchmark matrix spreadsheet.



Information on which compiler was used (vendor and version number)
must also be provided as additional information in the benchmark report, 
together with the modified makenek and makenek.inc scripts used.

## Contact

Questions regarding the benchmark(s) must be posted via the "question
and answer function" in Visma TendSign.

