# Benchmark suite for PDC procurement

This repository contains information about the benchmark suite used in
the procurement process for a new PDC system in 2020-2021.

The benchmark suite is composed of application benchmarks and
synthetic benchmarks. The list of application benchmarks contains
research software packages heavily used in HPC, i.e. Gromacs, Nek5000,
VASP, PowerFLOW, Converge, PyFR and CP2K-Quickstep, while the synthetic 
benchmarks include standard benchmark tests. See [the list of
benchmarks](./benchmarks/README.md) for an overview of all benchmarks
and corresponding subdirectories of the [benchmarks](./benchmarks)
folder for detailed documentation of each benchmark case.

The application benchmarks are either of a *throughput* and
*strong-scaling* type, which differ in how they should be run and in
the figure of merit on which they will be evaluated.  A detailed
description of how the figures of merit for each type of benchmark
should be obtained can be found in the document
[benchmark-description.pdf](./benchmark-description.pdf).

The list of synthetic benchmarks contains standard benchmark tests.
The result from these benchmarks will be used to reply to
should-requirements or must-requirements in the Tender document,
except for the FFT synthetic benchmark which will be used as a
throughput benchmark. For details see the
[benchmarks/Synthetic](./benchmarks/Synthetic) folder.

A benchmark report should accompany each tender. A list of
requirements on the benchmark report can be found in the Tender
document Part 5.

## Contact

Questions regarding the benchmark(s) must be posted via the "question
and answer function" in Visma TendSign.
