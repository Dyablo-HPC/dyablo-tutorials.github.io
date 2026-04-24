---
layout: page
title: Running Dyablo
permalink: /user_tutorials/running_dyablo
parent: User tutorials
nav_order: 2
---

# Running Dyablo

In this tutorial, we will see how to run Dyablo and to read the logs.

## Running directly Dyablo

The main executable dyablo is in `build/dyablo/bin/`. The directory also contains some `*.ini` files that can passed to the executable on the command line. For instance, to run a 2d Sedov-blast on the block-AMR backend, the command will be `./dyablo test_blast_2D_block.ini`. Other `.ini` files are available in the `settings/` directory at the root of the project.

Beware that when recompiling, the `.ini` files in bin may be reset to their original state.

Run for instance `./dyablo test_blast_3d_block.ini` to run the 3D block-base blast test case.

The log should display and if everything goes according to plan the run should end by displaying timers.

After a few seconds the run should be finished. The output log of Dyablo is generally made of four major section :

1. Kokkos' initialization step
2. Dyablo's initialization step
3. General run information
4. Timers reporting
  
### Kokkos' initialization step

The first elements logged by Dyablo are the information given by Kokkos. There can be a few warnings in the very first lines (for instance on the binding of OpenMP threads), then the actual configuration of Kokkos is displayed:  

```
##########################
KOKKOS CONFIG             
##########################
Kokkos configuration
  Kokkos Version: 5.1.0
Compiler:
  KOKKOS_COMPILER_GNU: 1240
Architecture:
  CPU architecture: none
  Default Device: OpenMP
  GPU architecture: none
  platform: 64bit
Atomics:
  desul atomics version: 5e8dd8b549c0de391acb77535a40aa5668c35d01
View:
  mdspan: enabled
  mdspan version: 5d4eb209c77f4744980c0b0c2af44636cc81b08b
Vectorization:
  KOKKOS_ENABLE_PRAGMA_IVDEP: no
  KOKKOS_ENABLE_PRAGMA_LOOPCOUNT: no
  KOKKOS_ENABLE_PRAGMA_UNROLL: no
  KOKKOS_ENABLE_PRAGMA_VECTOR: no
Memory:
Options:
  KOKKOS_ENABLE_ASM: yes
  KOKKOS_ENABLE_CXX20: yes
  KOKKOS_ENABLE_CXX23: no
  KOKKOS_ENABLE_CXX26: no
  KOKKOS_ENABLE_DEBUG_BOUNDS_CHECK: no
  KOKKOS_ENABLE_HWLOC: no
  KOKKOS_ENABLE_LIBDL: yes
Host Parallel Execution Space:
  KOKKOS_ENABLE_OPENMP: yes

OpenMP Runtime Configuration:
Kokkos::OpenMP thread_pool_topology[ 1 x 20 x 1 ]
```

This part is not really important for users unless you are trying to debug. The only really interesting part for now is the last line that indicates the distribution of threads.

```
Kokkos::OpenMP thread_pool_topology[ 1 x 20 x 1 ]
```

This for instance indicate that Dyablo is running on a cpu with one [NUMA](https://en.wikipedia.org/wiki/Non-uniform_memory_access) node, each NUMA node has 20 cores, each with a single thread.

If you don't know what that means, it simply indicates that we will have 20 threads available for calculations on this machine.

### Dyablo's initialization step

The second part of the log is Dyablo's initialization information:

```
##########################
AMRmesh initialized : 64 ( 0 ) 
Reallocate : add fields 0 -> 5
##########################
Godunov updater    : HydroUpdate_hancock
IO Manager         : IOManager_hdf5
Gravity solver     : none
Initial conditions : `blast` 
Refine condition   : RefineCondition_pseudo_gradient
Compute dt         : `Compute_dt_hydro` 
Source Terms : 
##########################
Output: scalar_data : iter=0 aexp=1 dt=1 time=0 
scalar_data : iter=0 aexp=1 dt=0.000638016 time=0 
Mesh - rank 0 octs : 1072 (0)
Reallocate : add fields 5 -> 10
```

The first part tells you the initial size of the mesh. When mesh sizes are given, they usually consists in two numbers one of which is in parenthesis. The first number is the number of octants in the current domain, the number in parenthesis is the number of octants that are MPI ghosts.

In this case, we have a single periodic domain, so we don't have any ghosts. The initial size of the mesh is 64 octants.

{: .warning }
> **Block-based AMR**
> 
> Dyablo is a block-based AMR code. That means the number of octants is NOT the number of cells in the domain. You will then have to multiply by the size of the blocks. In this specific example, we have `4x4x4` blocks, which means we have `64x4x4x4=4096` cells in the domain. More information about this in the [documentation](https://dyablo.readthedocs.io/en/latest/userguide/amr.html)

The next part indicates how many fields are being stored on the grid, in that case we go from 0 fields to 5 being allocated for hydrodynamics (density, three components for the momentum, and the total energy).

The next part indicates which plugins are used. Plugins are the basic bricks of Dyablo, they contain features that can be run in parallel on the grid. You will see more information on these in the [developer tutorials](../developer_tutorials).

In our example, we can see that: 

* Hydrodynamics is updated using a Muscl-Hancock scheme
* We write outputs using the xmf/hdf5 format
* We don't have gravity in the run
* We use the Sedov blast initial conditions
* We refine using a pseudo-gradient on density and energy
* The timestep is given by the traditional hydro CFL
* We don't have any source terms included

Finally, Dyablo makes an initial output, and initializes the grid. You see the number of fields doubling because we need to have additional space for writing the data of the next timestep.

The initialization stores the values and automatically applies refinement, which means we have now 1072 octants in the grid.

### General run information

Once initalization is finished, the run starts: 

```
scalar_data : iter=10 aexp=1 dt=0.000563627 time=0.00593247 
Mesh - rank 0 octs : 1408 (0)
scalar_data : iter=20 aexp=1 dt=0.000568343 time=0.0115475 
Mesh - rank 0 octs : 1464 (0)
scalar_data : iter=30 aexp=1 dt=0.000578316 time=0.017286 
Mesh - rank 0 octs : 1464 (0)
scalar_data : iter=40 aexp=1 dt=0.000586321 time=0.0231083 
Mesh - rank 0 octs : 1632 (0)
scalar_data : iter=50 aexp=1 dt=0.000590321 time=0.0289904 
Mesh - rank 0 octs : 1632 (0)
scalar_data : iter=60 aexp=1 dt=0.000591178 time=0.0348939 
Mesh - rank 0 octs : 1632 (0)
scalar_data : iter=70 aexp=1 dt=0.000595337 time=0.0408223 
Mesh - rank 0 octs : 1800 (0)
scalar_data : iter=80 aexp=1 dt=0.000601501 time=0.0468006 
Mesh - rank 0 octs : 1968 (0)
Final scalar_data : iter=86 aexp=1 dt=0.00018438 time=0.05
```

You can see two types of lines : 

1. **Regular iteration reporting**: By default, every 10 iterations, Dyablo prints a line to the log indicating the current time (iteration, time, expansion factor, and timestep).
2. **Mesh adaptation**: Every time the AMR cycle is triggered (here every 10 iterations), the mesh is adapted and the log is updated. Here we see the mesh grow as the blast expands.

### Timers

Finally, when the run is finished, timers are displayed on the output:

```
Total                     time (CPU) :     3.995 s (100.00%)
| Init                      time (CPU) :     0.017 s (  0.42%)
| | initial_conditions        time (CPU) :     0.016 s (  0.40%)
| | other                     time (CPU) :     0.001 s (  0.02%)
| TimeLoop                  time (CPU) :     3.960 s ( 99.12%)
| | AMR                       time (CPU) :     0.090 s (  2.25%)
| | | AMR: Mark cells           time (CPU) :     0.036 s (  0.90%)
| | | AMR: adapt                time (CPU) :     0.029 s (  0.72%)
| | | AMR: remap userdata       time (CPU) :     0.023 s (  0.58%)
| | | MPI ghosts                time (CPU) :     0.002 s (  0.04%)
| | | other                     time (CPU) :     0.000 s (  0.00%)
| | AMR: load-balance         time (CPU) :     0.003 s (  0.07%)
| | Hyperbolic_hancock        time (CPU) :     3.382 s ( 84.65%)
| | MPI ghosts                time (CPU) :     0.007 s (  0.18%)
| | checkpoint                time (CPU) :     0.000 s (  0.00%)
| | dt                        time (CPU) :     0.044 s (  1.09%)
| | outputs                   time (CPU) :     0.013 s (  0.32%)
| | other                     time (CPU) :     0.422 s ( 10.56%)
| checkpoint                time (CPU) :     0.005 s (  0.12%)
| outputs                   time (CPU) :     0.014 s (  0.34%)
| other                     time (CPU) :     0.000 s (  0.00%)
```

Timers are hierarchical and display the time on CPU and on GPU if you run with the GPU. Here we can see that most of the time is spent in the hydro update as expected.


## Running with multiple processes

In our example, we have run the code compield with the OpenMP backend, that means the code was running in parallel on CPU. As we have seen above, the machine had a single cpu with 20 cores, so Dyablo run with 20 threads. On some machines, it might be interesting to run with multiple processes, for instance if you have multiple nodes. You can use `mpirun` to run Dyablo: 

```bash
mpirun -np 2 ./dyablo test_blast_3d_block.ini
```

You will see that now, all adaptations and reallocation are given twice, once per MPI-process. That allows you to track which process has how many octants. 

{: .warning }
> **MPI processes and OpenMP threads**
> 
> Having multiple MPI processes on the same CPU AND OpenMP threads at the same time requires a proper binding of the threads on different cores to avoid oversubscription. This is not easy to do by hand, and thus we encourage you to use MPI only on a properly configured cluster with job-scheduling such as Slurm.

{: .note }
> **Timers**
> 
> When you run with multiple processes, the timers reported at the end are only for the MPI process of rank 0. That means that if there is a lot of load-imbalance, the timers might be biased. If you want a full report of the timing information, a `timers.txt` file is written at the end of the run, with one mpi rank per line.

## Running with SLURM

When using Dyablo on a cluster or a supercomputer, you will have to use a job scheduler. SLURM is the most common job-scheduler. Usually the configuration of the job-script will depend on the cluster so we don't document that here. Just note that to launch dyablo you will have to use `srun` instead of `mpirun`.



When you are ready, move to the third part of the tutorial on [the configuration file format](configuration_files).

