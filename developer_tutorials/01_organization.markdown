---
layout: page
title: Organization of the sources
permalink: /developer_tutorials/organization
parent: Developer tutorials
nav_order: 1
---

# General organization of the project

Before diving in the code, let's take a look at the root folder of Dyablo and how the files are organized.

Initially in the root folder, you should see the following files and folders: 

* `benchmarks`: A folder containing the performance and measurement benchmarks we do in Dyablo, let's forget these for the moment.
* `CMakeLists.txt`: The CMake file configuring the superbuild. We will talk about this in a minute
* `core`: The folder where **all the sources and tests** are stored. That is where we will be working in the following sections.
* `external`: This folder stores all the **external libraries** used by dyablo, this is where is stored Kokkos for instance.
* `LICENSE`: The license for using the code
* `Readme.md`: A markdown file with basic instructions for the code
* `settings`: A folder storing configuration files for basic runs in Dyablo

The essential of the code we will be modifying is located in the `core` folder, so let's take a look at what's inside: 

* `bin`: The folder where the **main of Dyablo** is located, as well as a couple of validation tests and paraview visualisation files.
* `cmake`: Various cmake helpers for compilation, ignore this.
* `CMakeLists.txt`: the cmake definitions for this folder, this one is dedicated to building Dyablo after all dependincies have been resolved.
* `doc`: The doxygen project for dyablo.
* `src`: The actual **sources for the plugins and features** of Dyablo
* `unit_test`: self-explanatory. The **unit tests** run by Dyablo.

The vast majority of the work you will be doing in Dyablo will happen in the `src` folder, so let's go a bit more in-depth there.

## The `src` folder

We will not go over every file in the folder, but let's look at a few important ones:

* `CMakeLists.txt`: The cmake definitions for this folder. This is where you generally add the files corresponding to your plugins so they get compiled and linked.
* `compute_dt`: The plugins computing the adaptive **timestepping**
* `hyperbolic`: The **schemes** and the **policies** [more on that in a bit] to solve the hyperbolic problems (hydro, MHD, RHD)
* `init`: All the **initial condition** plugins
* `parabolic`: The plugins solving explicit **parabolic equations** (thermal conduction, viscosity)
* `refine_conditions`: The plugins marking the cells for **(de)refinement**
* `source_terms`: Plugins implementing additional **source terms** for the equations

## The git repo

When cloning the repository of Dyablo, you are usually directly on the `dev` branch. `dev` is the branch that corresponds to the latest stage of the code, this is where you will get the regular updates and new features. Aside from this main branch, we can discriminate between two types of branches: 

* **Community branches** these are large branches with new plugins, and new features catered for specific projects. As of 2026, there are 3 community branches in Dyablo : `cosmo`, `ism` and `wholesun`. If you are not working on either of those subject, you can start a new branch with your own application directly from `dev`. Community branches have one or multiple **maintainers** that are here to review merge requests and do the general maintenance. 
* **Feature branches** these are branches that stem from `dev` or any community branch. They implement a new feature, fix a bug, improve some part of the code, etc. They are destined to be merged with their parent branch.

Generally speaking, core developments are made on feature branches that are merged to `dev`. Community branches have their own features that are merged into them, and regularly, the maintainers of the community branch will rebase the branch on `dev` to keep track of the latest updates and bugfixes. 

## General philosophy

Before we move on to plugins, let's talk a little bit about the general philosophy of developing Dyablo. The development of Dyablo is based on three pillars of software engineering: separation of concerns, high-level abstractions and modularity.

### Separation of concerns

Developing a high-performance code for the simulation of astrophysical fluids means having a lot of people collaborating on very different expertises. These expertises should not interfere with one another. And so, when developing Dyablo, we try to make sure that the HPC side of the code is isolated from the physics and mathematical side of the code. We try to enforce this as much as possible. That means that the HPC side of the code provides tools to the users, and that the physics side uses these tools. 

### High-level abstractions

To be able to do the separation of concerns efficiently, the physics side needs to be provided with adequate tools to use the grid, and calculate operations on the cells of the mesh. For this, we create high-level abstraction that hide the complexity of the underlying data. For instance, the `foreach_cell` construct allows the user to apply a treatment to every cell in the domain. The way the AMR data is traversed is irrelevant for the physics here. Another example is the `CellIndex` structure that represents a cell in the logical domain. Because the grid in Dyablo is not a regular grid, we cannot access a cell with a simple triplet of integer coordinates. Thus the `CellIndex` structure abstracts the position of the cell in the domain and allow you to look for the neighboring cell (that can be also multiple cells on a non-conformal face), access their position, size, etc. 

### Modularity

Because Dyablo is made for numerical experiments, often times it is necessary to change or tweak some treatments. It is also common to want to benchmark a new implementation against another, and compare the accuracy and speed of different solvers. That is why we use plugins (more on that in the next tutorial) : abstractions that allow you to change the behavior of the code for certain treatments at runtime. Plugins are compiled once, and then can be used in the code. Additionally, for plugins that are not in a dependence chain, changing the plugin will mean only recompiling this specific plugin which saves a lot of time.

## Good practice for writing code in Dyablo

Before starting developing anything in Dyablo, it is understood that you will follow a set of simple rules to make sure everything goes according to plan. Although the process can look a bit heavy at first it is there to make sure that you will not spend too much time developing things that will never be usable or merged into other branches. 

1. **Create a branch** dedicated to your feature. This branch should be made either on top of `dev`, or an application branch (`cosmo`, `ism`, `wholesun`).
2. Regularly **rebase your branch** to avoid large rebases that lead to a lot of conflicts.
3. **Before** starting to develop a cool new feature, check the issues if someone has already started to work on it. If so, contact them to help them develop, test or adapt their feature to your problem.
4. If none is open on the subject, **create an issue** and describe what you intend to do, how you plan to do it, and if you need help.
5. **Join the slack** to start discussing your issue/feature. For that, either request a invitation from another member, or if you don't know any, drop us a message on github/gitlab and we will invite you.
6. We have regular **Dyablo dev meetings** where we discuss the developments. Once you join slack you will receive the invitations for these meetings. They generally happen once a month during the day in Europe.

When you are ready, move on to the next part of the tutorial on [plugins](plugins)