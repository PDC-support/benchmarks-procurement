# PowerFLOW benchmark

3DS's PowerFLOW is a commercial CFD software for the simulation of
complex fluid flow problems. The conceptual approach used to solve
fluid flow problems in PowerFLOW software is significantly different
from the approach used in all other CFD codes. The main difference is
that while traditional CFD approaches start with a mathematical
description of a fluid at the continuum level (e.g. the Navier-Stokes
equations), PowerFLOW simulates fluids at a more fundamental, or
kinetic level via a discrete Boltzmann equation. PowerFLOW is widely
applied in transportation industry sectors, including automotive,
commercial vehicle and aircraft.

The benchmark case for PowerFLOW contains one test case which must
be run with version 6-2019 of PowerFLOW. The two necessary files are
located in the subdirectory BC_01.  In addition an example submit file
is included below adapted for a Cray system running SLURM.

## Source code

### Obtaining licenses and source code

The vendors should contact PDC (powerflow-benchmark@pdc.kth.se) to get
access to a RLM license server at PDC and the PowerFLOW source code
for the benchmarking test, if required. Version 6-2019 of Powerflow
must be used for the benchmark.

### Guidelines for Porting and Modifying the Source Code

To assure comparability and scientific correctness of the benchmark
results, modifications to the PowerFLOW source code are NOT permitted.

## Installation guide

The benchmark does not require any specific flags, options, or
environment variables to be compiled.

## Benchmark case

The benchmark case can be found under [BC_01/](./BC_01).
and consists of .cdi and .lgi files that constitute the complete case setup:
- BC_01_w1811.cdi
- BC_01_w1811.lgi

### Running benchmarks

Before starting PowerFLOW it may (on certain architectures) be necessary to 
modify the `exarun` script under `<installation_path>/dist/generic/tools/queue_scripts`. 
For questions on this please see the contact information below.

An example submit file `powerflow_simulate_fine_slurm.sh`
created for the Beskow XC-40 Cray cluster is as follows:

```bash
#!/bin/bash -l
#SBATCH -J Benchmark_PowerFLOW
#SBATCH -e slurm_errors.%J.log
#SBATCH -o slurm_output.%J.log
#SBATCH -t 24:00:00
# Number of nodes to be allocated is (NPROCS)/(NPROCS_PER_NODE)+1
#SBATCH -N 41
##SBATCH -N 21
##SBATCH -N 11
# Number of MPI processes per node (default 32)
#SBATCH --ntasks-per-node=32

# Number of cores for simulation
export NPROCS=1280
#export NPROCS=640
#export NPROCS=320

# Number of timesteps, must not be changed
export NUM_TIMESTEPS=10000

# load PowerFLOW module
module load powerflow/6-2019

exawatcher > powerflow_exawatcher_simulate_fine.$SLURM_JOBID.log &

exaqsub -xe6_decomp \
        -decompose \
        -decomp_log powerflow_decompose_fine.$SLURM_JOBID.log \
        -xe6 \
        -num_timesteps $NUM_TIMESTEPS \
        -foreground \
        -simulate \
        -sim_log powerflow_simulate_fine.$SLURM_JOBID.log \
        -simoption "-checkpoint 1000000" \
        -nprocs $NPROCS \
        --delay_vel_warnings 3000000 \
        *.cdi
```

Note that the `-xe6_decomp` and `-xe6` flags are specific for the 
Cray architecture of Beskow and should be removed or replaced by 
other flags for other architectures. 

The `exaqsub` command has many possible options that can be listed by
`exaqsum -h`. There is normally no need to modify any flags since
PowerFLOW automatically detects hardware and MPI configurations, but
it is still recommended to explicitly set the interconnect using
options from the "MPI Interconnect Options" section.  The Platform MPI
option (`--hpmpi`) from the "MPI Implementation Options" section is
default and normally performs better than the Intel MPI option.  Flags
listed in the "MPI Options" section are not recommended.

On a SLURM system the simulation can be started through the command:
`sbatch powerflow_simulate_fine_slurm.sh`

### Minimum performance

This PowerFLOW benchmark will be evaluated according to how many jobs
that can be performed on the total system in one day, according to the
throughput formula.

We require a *minimum performance*: the average runtime (simulation
time) must not be larger than 3600 seconds (i.e. "real time" must
not be larger than 3600 s).

### Allowed changes to input files

The .cdi and .lgi files must not be changed. The number of cores and nodes
need to be adjusted to fit on the system. Note that PowerFLOW requires
an extra node used as a masternode, see the example 
submit file above for details.

### Validation of results

After finishing a run go through the following step:

- Make sure that the file powerflow_exawatcher_simulate_fine.[jobid].log ends
  with lines similar to:
  ```
  Tue Jun  9 10:15:48 CEST 2020 Simulator: Finished 10000 Timesteps
  Tue Jun  9 10:15:48 CEST 2020 [Watcher terminated]
  ```

### Obtaining benchmark results

The execution time should be extracted from the file 
`powerflow_simulate_fine.[jobid].log`

The simulation time is right after "Simulation time". The value for
the initialization time found after "Initialization time" must also
be included in the total time.

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

`n_{jobs}` must be entered into the benchmark matrix spreadsheet.


## Contact

Questions regarding the benchmark(s) must be posted via the "question and answer function" in Visma TendSign.
