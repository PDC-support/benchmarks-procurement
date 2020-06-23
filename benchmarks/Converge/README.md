# Converge benchmark

This directory contains general information regarding the Converge
CFD software suite, installation, and benchmarking considerations. 

## Source code


### Obtaining the source code

The source code of Converge CFD should be obtained from Convergent Science.

### License

Converge is licensed software. To acquire licenses for Converge,
please, contact Chris Riley <chris.riley@convergecfd.com> with the
hostname and mac address of the license server, the number of solvers
required and the duration for the benchmark test. Please, also mention
that this is a benchmark together with Scania. Usually Convergent can
get back to you with licenses within a couple of days.

### Guidelines for Porting and Modifying the Source Code

To assure comparability and scientific correctness of the benchmark
results, modifications to the Converge CFD source code are NOT permitted.

## Installation guide

Installing ConvergeCFD is straightforward:
```bash
$ tar xvfz Convergent_Science_Full_Package-3.0.14.tar.gz
$ cd Convergent_Science_Full_Package-3.0.14
$ ./INSTALL
```

This will install pre-built binaries for different MPI 
libraries.


## Performance considerations

ConvergeCFD comes with pre-built binaries and vendors should only
select which MPI library to be used.

## Benchmark cases

This benchmark case should be run on varying number of nodes.
All required input and parameter files can be found in the 
[FlameD-50M/](./FlameD-50M) directory.

### Running the benchmark cases

Example run script:

```bash
module load <compilers>
export CVG_SOLVER_ROOT=/path/to/converge/3.0.14
export PATH=/path/to/converge/3.0.14/bin:$PATH
export CPATH=/path/to/converge/3.0.14/include:$CPATH
export LD_LIBRARY_PATH=/path/to/converge/3.0.14/lib64:$LD_LIBRARY_PATH
export LD_LIBRARY_PATH /path/to/mpi/lib:$LD_LIBRARY_PATH

# run on NPROCS cores with OpenMPI version:
mpirun -n $NPROCS converge-ompi super > log.converge.$SLURM_JOBID 2>&1
```

### Obtaining benchmark results

The benchmark must be run on the smallest number of nodes on which the benchmark runs and then using a steadily increasing number of nodes until the absolute performance stops improving. It will be assumed that any results using a larger number of nodes than is provided will not give improved performance.

Enough intermediate data points between the largest run and the smallest run must also be given so that the expected performance on any number of nodes on which the job could be run can be inferred through interpolation with reasonable confidence.

Both the best possible performance and results for each data point must be entered into the benchmark report.

## Contact

Questions regarding the benchmark(s) must be posted via the "question and answer function" in Visma TendSign.
