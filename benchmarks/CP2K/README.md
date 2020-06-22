# CP2K benchmark

This directory contains general information regarding the CP2K code,
installation, performance considerations and details on the DFT Linear 
Scaling benchmark case. Information specific to the CP2K DFT linear 
scaling benchmark case can be found in [this
page](https://github.com/cp2k/cp2k/blob/master/benchmarks/QS_DM_LS/README.md).

CP2K is an open source quantum chemistry and solid state physics
software package for atomistic simulations of a wide range of
materials.  It provides a general framework for DFT modelling methods
using mixed Gaussian and plane waves approaches GPW and GAPW.

## Source code

### Obtaining the source code

The official CP2K releases can be obtained from the [download
webpage](https://www.cp2k.org/download) and the [GitHub project
page](https://github.com/cp2k/cp2k/releases/).

For the benchmark the CP2K 7.x series should be used.

### License

CP2K is distributed under the GPL license.

### Guidelines for Porting and Modifying the Source Code

Modifications to the CP2K source code are acceptable as long as any
modifications are made available to the CP2K development team
before acceptance benchmark runs on the delivered system.
All modifications should be general and not problem-specific.
Modifications of the makefiles are permitted.


## Installation guide

For a detailed guide on prerequisites and how to install the software,
see the [CP2K installation
instructions](https://github.com/cp2k/cp2k/blob/master/INSTALL.md).

## Performance considerations

This benchmark is particularly sensitive to network performance and to
the performance of batched small matrix-matrix multiplications.
To obtain optimal performance for small matrix-matrix multiplications,
CP2K should be compiled with some of the libraries specified below.

- [libxsmm](https://github.com/hfp/libxsmm): a library for specialized
  dense and sparse matrix operations targetting Intel
  Architecture. More details on CP2K integration
  [here](https://github.com/cp2k/cp2k/blob/master/INSTALL.md#2i-libxsmm-optional-improved-performance-for-matrix-multiplication). Add
  ``-D__LIBXSMM`` flag to ``DFLAGS``.
- [libsmm](https://github.com/cp2k/dbcsr/tree/develop/tools/build_libsmm):
  a library for small matrix multiplies which relies on autotuning for
  achieving best performance on a given architecture. Autotuning this
  library for a specific hardware architecture might be necessary.
- [libsmm_acc](https://github.com/cp2k/dbcsr/tree/develop/src/acc/libsmm_acc)
  (previously known as "libcusmm"): near-optimal parameter sets are
  provided for some GPUs (Nvidia P100, Nvidia V100, and partially for
  AMD Mi50), and instructions are provided in DBCSR's repository for
  [autotuning](https://github.com/cp2k/dbcsr/tree/develop/src/acc/libsmm_acc/tune)
  and [near-optimal parameter
  prediction](https://github.com/cp2k/dbcsr/tree/develop/src/acc/libsmm_acc/predict).
- Relevant flags (e.g. ``-D__ACC`` and ``-D__DBCSR_ACC``) are needed
  to enable accelerator support.

CP2K can be compiled in different flavors - see
https://github.com/cp2k/cp2k/blob/master/INSTALL.md#3a-arch-files.
Most likely the hybrid MPI + OpenMP psmp version will give the best
performance.


## Benchmark case

There is one benchmark case for CP2K and it will be evaluated as a
throughput benchmark.  The case is a single-point energy calculation
using linear-scaling DFT for a cubic periodic supercell of side length
39 Angstrom and containing 2048 water molecules (6144 atoms). An LDA
functional is used with a DZVP MOLOPT basis set and a 300 Ry cut-off.

- The input file `H2O-dft-ls.NREP4.inp` is found under [H2O-dft-ls](./H2O-dft-ls)

*No input parameters can be changed.*
Note that the "NREP=4" parameter in the input file specifies the
system size.


### Running the benchmark case

To run the benchmark, simply do:
```
$ cd H2O-dft-ls
$ mpirun/srun/similar [options] cp2k.psmp -i H2O-dft-ls.NREP4.inp -o output.out
```

### Minimum performance

This CP2K benchmark will be evaluated according to how many jobs can 
be performed on the total system in one day, according to the 
throughput formula.

We require a minimum performance: the average runtime should not be
larger than 150 seconds (i.e. "TOTAL TIME / MAXIMUM" for the "CP2K"
entry should not be larger than 150 s).


### Obtaining benchmark results

CP2K prints timing information at the end of the output file.  The
first line under the "T I M I N G" section contains "CP2K", and the
final column (TOTAL TIME / MAXIMUM) contains the relevant time in
seconds.

This benchmark will be evaluated by the throughput metric, i.e. how
many jobs can be completed on the tendered system in one day. 
This number should represent 
an average and does not need to be an integer.
To calculate the number of jobs per day the throughput formula 
should be used:  
`n_{jobs} = t_{day} / t * N_{tot} / N`

where
- `t_{day}` = time in a day (86400 s)
- `t` = average time to run one job for this benchmark
- `N_{tot}` = total number of nodes tendered
- `N` = number of nodes used to run this benchmark
- `n_{jobs}` = number of jobs that can be run in a day for this benchmark 

`n_{jobs}` should be entered into the benchmark matrix spreadsheet.


## Contact

Questions regarding the benchmark(s) must be posted via the "question and answer function" in Visma TendSign.
