# GROMACS Aquaporin ensemble benchmark

For a description of this case please see the [benchmark README
file](./inputs//README)

## Input files

### Benchmark input files

- The main simulation input file is [topol.tpr](./inputs/topol.tpr)
- The full set of original input files is included in the 
  [input directory](./inputs/) and the ``topol.tpr`` input file can
  re-generated using the ``gmx grompp`` tool;
- Ensemble setup directory tree is provided in the
  [ensemble_inputs.tar.gz archive](./inputs/ensemble_inputs.tar.gz)

### Generating the inputs for the ensemble run

For this benchmark an ensemble run, called multi-simulation in
GROMACS, is used. The supplied example uses a 32 member ensemble, and
this is the number of ensemble members that must be used. The
ensemble simulation mode of the ``mdrun`` tool requires one directory
per ensemble member simulation.  As the AWH algorithm employed uses
identical input files, a single tpr input can be created and symlinked
in each of the output directories.

The (bash) shell script code below shows the steps to re-create
the 32 inputs which are provided in
[ensemble_setup.tar.gz archive](./inputs/ensemble_setup.tar.gz).
```
gmx grompp -n index.ndx -o topol.tpr
for i in {01..32}; do
  dirname=repl_${i}
  ( mkdir $dirname && cd $dirname && ln -s ../topol.tpr topol.tpr; )
done
```

## How to run the ensemble benchmarks

To run the ensemble benchmark, launch a total of N*32 ranks and pass
the 32 output directories to the mdrun ``-multidir`` option. As a
result, N ranks per simulation will be used (ranks assigned
sequentially).  Set a runtime length in wall-time and use counter
resetting to record performance after the load balancing phase is
complete and the hardware "warms up".

An example command for a 32-rank run (i.e. one rank per simulation):
`mpirun -np 32 gmx_mpi mdrun -multidir repl_* -nsteps 1000000 -resethway`

Note that each of the member simulations will have its performance
reported in its own log file.

## Performance aspects

The simulation system used for this benchmark consists of
approximately 100000 atoms.  When domain-decomposed a significant
challenge is the inhomogeneous work distribution across domains.

The ensemble run is scheduled as a single job submission, but it is
composed of coarsely communicating members (every 1000 iterations)
which in turn have significantly latency-sensitive communication
internally within a simulation.  This poses the challenges to node
allocation as ensemble member simulations greatly benefit from being
assigned their ranks to a "compact" set of nodes whereas nodes
assigned to different simulations can be "further" apart.  In
addition, because simulations communicate at fixed iteration
intervals, the slowest of the simulations will limit the performance
of the entire run.  Therefore, to avoid such imbalance it is advisable
to minimize bottlenecks that can cause systematic discrepancies in
simulation throughput across the ensemble.

## How to quantify benchmark performance

The benchmark can be run on any amount of resources which satisfies 
the following minimum performance constraints:
- On phase 1, the performance per replica must not be lower than 80 ns/day
- On phase 2, the performance per replica must not be lower than 200 ns/day

It is not necessary to fully populate the nodes if it gives superior
performance, but this must be clearly explained in the benchmark
report.

Vendors may make a free choice of the number of PME nodes that can
affect performance.
