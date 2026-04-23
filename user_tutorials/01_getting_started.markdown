---
layout: page
title: Getting started
permalink: /user_tutorials/getting_started
parent: User tutorials
nav_order: 1
---

# Getting started with Dyablo

In this section, we will see **what Dyablo is**, how to **get the code** and how to **compile it**.

## What is Dyablo ?

Dyablo is a modern C++ code for the simulation of astrophysical plasmas on adaptive (AMR) grids. Dyablo relies on the [Kokkos](https://github.com/kokkos/kokkos) framework for performance portability. That means that Dyablo can run on a machine with a CPU, or on GPUs regardless of the vendor. 

## Prerequisites

For Dyablo to compile, you require a few pre-requisites: 

1. A C++ compiler with C++20 capabilities. Dyablo has been tested with `gcc` 12.3+ and `clang` 12+.
2. A `cmake` 3.16+
3. HDF5 compiled in parallel mode, version 1.10.
4. MPI
5. `libxml2`

## Getting the code

Dyablo is publicly available on [github](https://github.com/Dyablo-HPC/Dyablo). When cloning Dyablo it is important to clone also the submodules. In an appropriate folder run the following git command:

```bash
git clone --recurse-submodules https://github.com/Dyablo-HPC/Dyablo.git
```

This will create a `dyablo` folder with all the sources. If you forgot to add the `--recurse-submodules` options, you can still initialize the submodules by running the following commands:

```bash
git submodule init
git submodule update
```

## Building for CPU

To make a first build of Dyablo, create a build subfolder and configure the CMake project:

```bash
mkdir build_cpu; build_cpu
cmake -DCMAKE_BUILD_TYPE=Release ..
```

{: .warning }
> If you are a Mac user, chances are you will get an error about OpenMP. The problem here is that Apple creates automatically an alias on the command gcc that points to their own version of clang (AppleClang) which is not compatible with OpenMP. The solution is to install a real gcc separately and use this on the configuration line : `CC=gcc CXX=g++ cmake -DCMAKE_BUILD_TYPE=Release ..` .

By default, if no other argument is provided to the CMake command, Dyablo will build using the OpenMP backend of Kokkos, effectively compiling for multi-threaded CPUs.

Once the configuration step finished, the last three lines should look like the following:

```
-- Configuring done (1.9s)
-- Generating done (0.0s)
-- Build files have been written to: <path_to_your_build_folder>
```

You can then compile Dyablo by using `make`. As much as possible, try to compile with multi-threading. For instance, if your machine has 8 cores available, run: 

```bash
make -j 8
```

The code should take a little while to compile, but will eventually end-up with the following statement:

```
[100%] Completed 'dyablo'
[100%] Built target dyablo
```

There should be a `dyablo` executable binary in the `dyablo/bin` folder.

## Building for GPU

Alternatively, you can configure Dyablo to compile for a GPU target. For a Nvidia GPU with cuda, the configuration line will become :

```bash
cmake -DCMAKE_BUILD_TYPE=Release -DKokkos_ENABLE_CUDA=ON ..
```

while for an AMD GPU the configuration will use HIP :

```bash
cmake -DCMAKE_BUILD_TYPE=Release -DKokkos_ENABLE_HIP=ON ..
```

For these two configurations, Kokkos should be able to automatically detect the architecture used by your GPU. 

Compilation then works the same way as for cpu build.

When compilation is finished, you can move to the next tutorial on [how to run a dyablo simulation](/user_tutorials/running_dyablo).


When you are ready, move to the second part of the tutorial on [how to run Dyablo](/user_tutorials/running_dyablo).
