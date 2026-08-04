# poisson-solver performance evaluation
This readme explains the performance evaluation process of `poisson-solver` and comparison with `PoisFFT`.

## poisson-solver
### Local
`poisson-solver` is modified in the `benchmarks` branch to accomodate for this evaluation.
The benchmarks and runners for them are also present in that branch.

The following metrics will be measured:
1. Buffer copy duration
    - Host->device and device->host or
    - in case device buffers are supplied (via GPU API), the device<->device copy.
        - (Device<->device copies are used to slice off ghost cells)
2. FFT execution duration
3. Kernel execution duration
4. Total time of `Execute()` (or `ExecuteGPU()`)

The `run_local_benchmarks.sh` script will run these measurements from the cluster head node.
It measures the metrics for solvers with different combinations of:
1. Precision (double/float)
2. GPU API (yes/no)
    - `poisson-solver` provides additional API to supply device buffers directly
3. Dimensionality (1/2/3)
4. Uniformity of Z axis
    - `poisson-solver` allows users to define nonuniform Z axis
5. Boundary conditions
    - Depend on dimension and uniformity of Z axis

### Distributed
TODO - Not finished, only rough ideas
- Measure only 3D solvers, distributed solvers won't realistically be run on <3D problems.
- Measure strong scaling with increasing amount of nodes.
    - Node selection? I don't think we have many BW nodes.
- Measure weak scaling with increasing amount of nodes.

## PoisFFT
### Local
[This fork](https://github.com/mntjams/PoisFFT) of `PoisFFT` contains the changes for the evaluation along with the benchmarks themselves and runners for them.
Only the _total time to execute one iteration_ is measured as `PoisFFT` software architecture makes it very tedious to incorporate all the metrics present in `poisson-solver` evaluation.

The runner for `PoisFFT` evaluation also executes on all the different combinations of solvers (apart from those not supported by `PoisFFT`, e.g., GPU API).
In addition to those it also runs with 1, 2, 4, 8, 16, 32 and 64 cores.

### Distributed
TODO - Also not finished
- Should be basically identical with distributed `poisson-solver` evaluation (with the limitations presented in the previous section)

## ELMM
TODO - How to measure in ELMM?
- ELMM already outputs the _total time to execute a simulation_ and _time spent in Poisson solver_
    - It's very bare-bones but maybe it can be used?
