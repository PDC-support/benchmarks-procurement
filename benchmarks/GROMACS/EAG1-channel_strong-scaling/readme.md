# GROMACS strong scaling benchmark

For a description of this case please see the [benchmark README file](./inputs/README)

## Input files

### Benchmark input files

- the main simulation input file is [topol.tpr](./inputs/topol.tpr)
- the above input file can re-generated using the ``gmx grompp -n index.ndx`` as the full set of original input files is included in the [input directory](./inputs/)

## Performance aspects

The simulation system used for this benchmark consists of approximately 616000 atoms.
When domain-decomposed inhomegeneous work distribution across domains poses a challenge
for strong scaling. Additionally, at high scale wider MPI ranks and tuning of
separate PME ranks is required in order to balance the PP-PME load.


## How to quantify benchmark performance

The benchmark must be run on the smallest number of nodes on which the
benchmark runs (which is expected to be one) and then using a steadily
increasing number of nodes until the absolute performance (in ns/day)
stops improving. It will be assumed that any results using a larger
number of nodes than is provided will not give improved performance.

Enough intermediate data points between the largest run and the
smallest run must also be given so that the expected performance on
any number of nodes on which the job could be run can be inferred
through interpolation with reasonable confidence.  

Only the best possible performance must be entered into the 
benchmark matrix spreadsheet, but results for each data point 
must be entered into the benchmark report.

It is not necessary to fully populate the nodes if it gives superior
performance, but this must be clearly explained in the benchmark
report.

Vendors may make a free choice of the number of PME nodes that can
affect performance.
