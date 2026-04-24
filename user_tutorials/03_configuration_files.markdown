---
layout: page
title: Configuration files
permalink: /user_tutorials/configuration_files
parent: User tutorials
nav_order: 3
---

# Configuration files

In this section we will see what the basic configuration files are, how they are structured and the main sections you will have to define.

## `.ini` files

[`.ini` files](https://en.wikipedia.org/wiki/INI_file) are very common configuration files. They are structured in sections (`[run]`, `[amr]`, `[mesh]`, etc.) and inside these sections you can define pairs of **keys** and **values**. A typical section will thus look like this: 

```ini
[section1]
key1=value1
key2=value2

[section2]
key3=value3
```

Let's take a look at the `test_blast_3D_block.ini` configuration file we used earlier. This file is structured as most `.ini` files for Dyablo, and thus the sections present here are very common. Let's go over these in order

### The `[run]` section

This section represents all there is to know about the current run: what the initial conditions are, how long should it run, how often should we write outputs and checkpoints, etc.

In our example, we have the following section: 

```ini
[run]
tEnd=0.05
nStepmax=1000
nOutput=1
initial_conditions=blast
```

`tEnd` reprends the time in code units when we should end the run and `nStepmax` is the total number of steps allowed. The run will end whenever one of these conditions are met. 

`nOutput` is a deprecated argument that has been left here for teaching purpose (more on that below in the section on `last.ini`). Finally `initial_conditions` is self-explanatory. We indicate here that we want to initialize the domain with a blast. 

### The `[amr]` section

This section describes all there is to do with the adaptive mesh refinement in the run.

```ini
level_min=2
level_max=4

bx=4
by=4
bz=4

nbOctsPerGroup=2048
loadbalance_coherent_levels=2

markers_kernel=RefineCondition_pseudo_gradient
epsilon_refine=0.1
epsilon_coarsen=0.05

load_balancing_frequency=50
cycle_frequency=10
```

`level_min` and `level_max` indicate the minimum and maximum refinement levels of the grid while `bx`, `by` and `bz` are the size of the AMR blocks. That means, the grid is refined at minimum level to a resolution of `2^level_min x [bx, by, bz]`. Here our blocks are of size `[4, 4, 4]` which means the coarser grid is of size `[16, 16, 16]`.  

The two following parameters are advanced parameters and should be left as they are for now.

Then, we have three parameters defining the refinement strategy. Here, we use pseudo-gradient on density and total energy. That means we take the absolute value of the difference between a cell and its neighbor, divided it by the maximum value of the two, and refine if the final value is above a certain threshold `epsilon_refine`. We derefine if the value is below another threshole `epsilon_coarsen`.

{: .warning }
> **Refinement and blocks**
>
> Refinement happens at the octant level, but the calculations are done at the cell level. That means we calculate the refinement criterion for each cell insode a block. If any cell is to be refined, then refinement takes the priority and the whole octant is refined. If no cells needs to be refined but at least a cell is marked as "stay as is", then the octant stays at the same level. Finally if **all** the cells in the block are marked as "derefining" then, the octant gets derefined.

The last two parameters indicate how many iterations separate two load-balancings and two AMR cycles.

### The `[mesh]` section

This section describes the size of the computation domain. It is mostly self-explanatory. The only thing you might want to play with right now are the boundary conditions that can be set as `periodic`, `reflective` or `absorbing`.

Following that section are a couple of sections we won't detail: the `hydro` and `blast` sections which are mostly physics related.

### The `[output]` section

The last section of the file is the `output` section which defines where and how to write outputs for the run. 

The `outputDir` key indicates where to write the outputs, 

`outputPrefix` gives a name to the outputs. In our case the prefix is `test_blast_3D_block` which means all outputs will be of the form `test_blast_3D_block_iterXXXXXXX.xmf|h5`. 

Finally the `write_variables` entry indicates which variables should be stored in the outputs. For our example, we store all the conservative variables.

## Additional notes on the `.ini` files

The `.ini` file has a lot of default entries and sections. Every plugin in dyablo will read from this file. If you wish to use the default behavior, you can just leave the sections empty and Dyablo will tell you if a value is actually required.

## Units

Dyablo works with internal code units. These code units can be specified in the `.ini` file in the section `[units]`. By default, these units are in the SI system. All calculations in the code are made using these code units. All values in the `.ini` file not indicating units are taken in code units. For instance, the size of the domain is always given in code units.

Some plugins will require you to enter values in some units. For instance a temperature or a density. Let's take for instance a default density `rho0` for some plugin. Dyablo will expect either no units specified on the line (which means it will be read as code-units for the density, by default this is kg/m^3), or a unit of density:

```ini
[my_plugin]
rho0=100.0      # 100 kg/m^3 OK
rho0=100.0g/cm3 # 100 g/cm^3 OK
rho0=100m       # invalid units -> runtime error
```

Units are not always asked for and should be only required for very specific plugins. You should always make sure you define correctly your code units so that your run makes sense in terms of physics.

## `last.ini`

Last but not least, sometimes remembering all the parameters to a run can be difficult. When Dyablo runs, after initialization, it writes a file `last.ini` registering all the values that have been read, ignored or taken as default values by Dyablo. Let's take a look at the `[run]` section of the `last.ini` file generated by the blast run. We can see a lot of default parameters have been used in the code. At the same time, the bogus parameter `noutput` is unused by Dyablo and is marked as such. 

Everytime you start a run, you should take a look at the `last.ini` file so you make sure you have not misspelled or forgotten a parameter.


When you are ready, move to the fourth part of the tutorial on [how to analyze Dyablo outputs](analyzing).
