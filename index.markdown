---
layout: page
title: Home
nav_order: 1
---

# Welcome to the Dyablo tutorials page

This site is dedicated to the astrophysics simulation code [Dyablo](https://github.com/Dyablo-HPC/Dyablo). 
Dyablo is a modern C++ code based on the performance portability framework [Kokkos](https://github.com/kokkos/kokkos). Dyablo is dedicated to the simulation of astrophysical plasmas using hierarchical, block-based Cartesian adaptive mesh refinement (AMR). 

{: .warning }
> This page is still a work-in-progress. Feel free to contact us if you spot any bug or problem.

The following tutorials are separated in two parts. The first part is dedicated on using the code, how to compile it, how to run it, and how to plot results. The second part is dedicated to adding new features to Dyablo.

## Table of content

### [1. User tutorials](user_tutorials/)
 - [Getting and compiling the code](user_tutorials/getting_started)
 - [Running a sample simulation](user_tutorials/running_dyablo)
 - [Configuration files](user_tutorials/ini_file)
 - [Visualizing and analyzing the results of a simulation](user_tutorials/analyzing)
 - [Restarting a simulation](user_tutorials/restarting)

### [2. Developer tutorials](developer_tutorials/)
 - [Organization of the sources](developer_tutorials/organization)
 - [Plugins in Dyablo](developer_tutorials/plugins)
 - [Creating an initial condition](developer_tutorials/initial_conditions)
 - [Adding a source-term](developer_tutorials/source_term)