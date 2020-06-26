# GROMACS benchmarks

This directory contains general information regarding the GROMACS
code, installation, and benchmarking considerations.  Information
specific to the GROMACS benchmark suite are to be found in the
subdirectories [aqp_ensemble](./aqp_ensemble) and
[EAG1-channel_strong-scaling](./EAG1-channel_strong-scaling).

Gromacs is a highly efficient and optimized code for Molecular
Dynamics simulations developed by groups at KTH and Uppsala
University. The software is compliant with the C99 and C++11 standards
and supports many hardware specific extensions such as SSE and
AVX. The code can make efficient use of GPUs as well. FFTW is the only
definitely required external library but the Gromacs build system is
able to compile it on demand.

## Source code

### Obtaining the source code

The official GROMACS releases are distributed through source
packages and can be obtained from the [releases
webpage](http://manual.gromacs.org) with the most recent release under
the ["Latest releases
section"](http://manual.gromacs.org/#latest-releases).

**For the benchmarks the GROMACS 2020.1 release or more recent must be used.**

### License

GROMACS is free software, distributed under the GNU Lesser General
Public License (LGPL) Version 2.1; for further details see the
``COPYING`` file in the root of the source distribution.

### Guidelines for Porting and Modifying the Source Code

Modifications to the GROMACS source code are acceptable as long as any
modification, for example new compute kernels or optimizations to
existing kernels, must be made available for integration in the
GROMACS source code.  Specifically, the winning vendor must provide
any modifications formatted to follow the [GROMACS contribution
guidelines](http://manual.gromacs.org/documentation/current/dev-manual/contribute.html),
and submit them for [code
review](http://manual.gromacs.org/documentation/current/dev-manual/change-management.html).
Having the contributions committed and merged is not a requirement for
acceptance, however the contributions must be made available to the
GROMACS development team before acceptance benchmark runs on the
delivered system.

## Installation guide

For a detailed guide on prerequisites, how to configure and install
the software, see the [official GROMACS installation
guide](http://manual.gromacs.org/documentation/current/install-guide/index.html).

For best performance, it is recommended to use a recent compiler
toolchain. Particular care should be taken to ensure that the [right
SIMD instruction
set](http://manual.gromacs.org/documentation/current/install-guide/index.html#simd-support)
is enabled at configure time.

To validate the installation, follow the instruction on how to test
[GROMACS for
correctness](http://manual.gromacs.org/documentation/2020/install-guide/index.html#testing-gromacs-for-correctness).

Note that if the official source distribution is altered (e.g. as a
result of optimization efforts), the configuration scripts will detect
this and will tag the GROMACS internal version string with a
`MODIFIED` suffix. We recommend that in such cases [a custom version
string suffix is
used](http://manual.gromacs.org/documentation/2020/install-guide/index.html#validating-gromacs-for-source-code-modifications).

## Performance considerations

GROMACS supports running on a wide range of hardware and targets
multiple levels of hardware parallelism.  For inter-node
parallelization MPI is used both SPMD and
[MPMD](http://manual.gromacs.org/documentation/2020/user-guide/mdrun-performance.html#).
Intra-node parallelization is done using SIMD compiler intrinsics,
OpenMP multi-threading as well as CUDA and OpenCL for heterogeneous
parallelization using GPU acceleration. Note that starting with the
2020 release, in single-GPU throughput runs the GPU acceleration
supports a fully-GPU resident loop for the force-compute steps in
CUDA.

For details on how to obtain the best performance, the reader is
recommended to consult the [relevant section of the GROMACS user
guide](http://manual.gromacs.org/documentation/2020/user-guide/mdrun-performance.html#).

## Benchmark cases

The PDC procurement benchmark suite includes two GROMACS benchmark
cases:

- [ensemble benchmark](./aqp_ensemble)
- [strong scaling benchmark](./EAG1-channel_strong-scaling)

Further information about these benchmark cases is provided in README
files provided in the subdirectories.


### Running the benchmark cases

For general instructions on how to execute the GROMACS ``mdrun``
simulation tool, see related instructions, examples and performance
checklist included in the [GROMACS performance
guide](http://manual.gromacs.org/documentation/2020/user-guide/mdrun-performance.html#running-mdrun-within-a-single-node).

### Minimum performance

The ensemble benchmark will be evaluated as a throughput benchmark 
and thus requires a minimum performance:

- On phase 1, the performance of an individual run must not be 
  less than 80 ns/day.
- On phase 2, the performance of an individual run must not be 
  less than 200 ns/day.

The strong scaling benchmark does not have a minimum performance.

### Obtaining benchmark results

The ``mdrun`` simulation tool prints a performance report at the end
of its log output.  This includes both wall-time and "ns/day" (the
simulation throughput expressed as simulated nanosecond per 24 hours),
the latter being the preferred metric reported in the PDC benchmarks
as it is independent of the wall-time of the benchmark execution.
This number can be easily parsed from the second first column of the
row starting with "Performance:" at the end of the ``mdrun`` log.

The simulation length can be specified using either:
- as the number of iterations with the ``-nsteps N`` option or
- as the simulation wall-time in hours using ``-nsteps -1 -max T``.
Note that benchmarks must be executed for sufficiently long time
such that both factors external (e.g. processor temperature and clock
throttle) as well as internal to simulation (e.g. load-balancer) reach
a stable state.  It is beneficial to exclude this short initial phase
especially from shorter measurements by resetting the internal timers
using the `-resethway` (or the `-resetstep N`) option.

The ensemble benchmark will be evaluated by the throughput metric,
i.e. how many jobs can be completed on the tendered system (separately
on phase 1 and phase 2) in a one day. This number should represent an
average and does not need to be an integer.
**One job for this ensemble benchmark is considered to be a 10 ns simulation.**
To calculate the number of jobs per day the throughput formula 
must be used:  
`n_{jobs} = t_{day} / t * N_{tot} / N`  
where
- `t_{day}` = time in a day (86400 s)
- `t` = average time to run one job for this benchmark
- `N_{tot}` = total number of nodes tendered
- `N` = number of nodes used to run this benchmark
- `n_{jobs}` = number of jobs that can be run in a day for this benchmark 

However, the factor `t_{day} / t` can be expressed as 
`Performance(ns/day) / 10 ns` because the time in seconds for one job is 
`t = 10 ns / Performance(ns/day) * 86400 s/day`.  
For example, if the observed performance 
of a run is 90 ns/day, the factor `t_{day} / t` becomes  
`86400 s / (10 ns / 90 ns/day * 86400 s/day) = 90/10 = 9`.  
To calculate the throughput metric `n_{jobs}`, this factor should 
then be multiplied by `N_{tot} / N`. The resulting value must be 
entered into the benchmark matrix spreadsheet, separately for phase 
1 and phase 2.

The strong scaling benchmark case will be evaluated by the
strong-scaling metric, i.e. how many jobs can be completed on the
tendered system (separately on phase 1 and phase 2 ) in one day given
that only one job runs at the time, i.e. the best possible performance 
that can be achieved regardless of resources used. 
This number should represent an
average and does not need to be an integer.  **One job for this
strong-scaling benchmark is considered to be a 10 ns simulation**.  
To calculate the number of jobs per day the strong-scaling formula
must be used:  
`n_{jobs} = t_{day} / t`   
where 
- t_{day} = time in a day (86400 s)
- t = average time to run one job (last 20 timesteps) for this benchmark
- n_{jobs} = number of jobs that can be run in one day for this benchmark

In the same manner as for the throughput ensemble benchmark, the factor 
`t_{day} / t` can be expressed as `Performance(ns/day) / 10 ns`. For 
example, if the best achievable performance is 250 ns/day, the final 
strong-scaling metric is `n_{jobs} = 250 / 10 = 25`. This number must 
be entered into the benchmark matrix spreadsheet, separately for phase 1 
and phase 2.

## Contact

Questions regarding the benchmark(s) must be posted via the "question and answer function" in Visma TendSign.
