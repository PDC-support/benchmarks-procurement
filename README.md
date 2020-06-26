# Benchmark suite for PDC procurement

This repository contains information about the benchmark suite used in
the procurement process for a new PDC system in 2020-2021.

The benchmark suite is composed of *throughput* and *strong-scaling*
benchmark cases which differ in how they should be run and in the
figure of merit on which they will be evaluated.  A detailed
description of how the figures of merit for each type of benchmark
should be obtained can be found in the document
[benchmark-description.pdf](./benchmark-description.pdf).

A score will be awarded to tenders based on reported performance on
all application benchmarks as well as the SingleFFT synthetic
benchmark.  Each application benchmark case has been assigned a
maximum number of points based on historical usage of PDC and SNIC
resources. A list of available maximum points and a description of the
scoring and ranking process is provided in the procurement document.

The benchmark suite is composed of application benchmarks and
synthetic benchmarks. The list of application benchmarks contains
research software packages heavily used in HPC, i.e. Gromacs, Nek5000,
VASP, PowerFLOW, Converge, PyFR and CP2K-Quickstep.  See [the list of
benchmarks](./benchmarks/README.md) for an overview of all benchmarks
and corresponding subdirectories of the [benchmarks](./benchmarks)
folder for detailed documentation of each benchmark case.

The list of synthetic benchmarks contains standard benchmark tests.
These will be evaluated as should-requirements or must-requirements.
For details see the [benchmarks/Synthetic](./benchmarks/Synthetic)
folder.

A benchmark report should accompany each tender. A list of
requirements on the benchmark report can be found in the procurement
document.

## Contact

Questions regarding the benchmark(s) must be posted via the "question
and answer function" in Visma TendSign.
