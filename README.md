# Benchmark suite for PDC procurement

This repository contains information about the benchmark suite 
used in the procurement process for a new PDC system in 2020-2021.

The benchmark suite is composed of *throughput* and *strong-scaling*
benchmark cases which differ in how they should be run and in the 
figure of merit on which they will be evaluated.  
A detailed description of how the figures of merit for each 
type of benchmark should be obtained can be found in the document
[benchmark-description.pdf](./benchmark-description.pdf).

A score will be awarded to tenders based on reported performance on all
application benchmarks as well as the SingleFFT synthetic benchmark.
Each application benchmark case has been assigned a maximum number of
points based on historical usage of PDC and SNIC resources. 
A description of the scoring and ranking process is provided in the 
procurement document.

The benchmark suite is composed of application benchmarks and
synthetic benchmarks. The list of application benchmarks contains
research software packages heavily used in HPC, i.e. Gromacs, 
Nek5000, VASP, PowerFLOW, Converge, PyFR and CP2K-Quickstep, 
see [the list of application benchmarks](application-benchmarks.md)
for an overview of all application benchmark cases.


The list of synthetic benchmarks contains standard benchmark tests.
These will be evaluated as should-requirements or must-requirements.


## Synthetic benchmarks

Synthetic benchmarks must be run on both Phase 1 and Phase 2.
More details can be found in 
[this folder](./benchmarks/Synthetic/).

- Stream Triad
  - Tests memory bandwidth of primary memory.
  - Verifying a should-requirement in section XXX.
- SingleFFT
  - Tests speed of the Fast Fourier Transform.
  - Evaluated in the same way as application benchmarks.
- HPL
  - Verifying must and should-requirements in section XXX and XXX.
- GPCNET
  - Global Performance and Congestion Network Test.
  - Verifying must-requirements in section XXX and XXX.
- IOR
  - Test read and write speed of a filesystem.
  - Verifying must-requirements in section XXX.
- Mdtest
  - Tests metadata operations of a filesystem.
  - Verifying must-requirements in section XXX.


## Benchmark report

Each tender must include a benchmark report. This report 
must contain:

- A description of how benchmark software was compiled if 
  the compilation deviated from the standard, e.g. any additional 
  compilation flags that were used.
- A description of any code modifications for software where such 
  changes are permitted.
- A list of permitted runtime options and flags that were 
  used to generate the benchmark results.
- The number of nodes/devices/cores-per-node etc. 
  used when generating benchmark results.
- A description of how the reported benchmark results were 
  estimated or predicted.
- The original benchmark performance results before converting 
  to the figure of merits (number of jobs per day).
- Any additional information that is requested in the documentation 
  of the benchmark cases.
