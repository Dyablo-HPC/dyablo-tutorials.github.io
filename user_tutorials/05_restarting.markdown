---
layout: page
title: Restarting a run
permalink: /user_tutorials/restarting
parent: User tutorials
nav_order: 5
---

# Restarting a run

In this last user tutorial, we will see how to do checkpointing and restart a run after completion or after a crash.
By defaultin Dyablo a restart file is written at the end of a successfully completed run. All checkpoints are written in a couple of files (`.ini` and `.h5` file) and have a name with the format `restart_PREFIX_ITERATION.h5|.ini`. 

With our blast run, we should have a restart file around iteration 86. Let's go ahead and open the corresponding `.ini` file. We can see that, as for the `last.ini` file, all the variables are being dumped in the `.ini` file. Now let's make that run a bit longer by pushing the end time to 0.2. Locate the `[run]` section and edit the `tend` value so it is 0.2 now. Then, restart the run by feeding this `.ini` file to dyablo : 

```bash
./dyablo restart_test_blast_3D_block_86.ini
```

The run restarts and shoudl take a few tens of seconds to run. When finished you should have a new snapshot. Rendering it with Paraview or pyablo you give you the following result : 

![The blast has moved even more and is now bounding on the box](/imgs/10_pyablo_2.png)

## A couple of interesting parameters

When doing a restart, you can play with a few parameters in the `[run]` section of the `.ini` file to fine-tune what you want to do. First of all, you can change as we did the value of the end time of the run, and also the maximum number of iterations. You can also decide to reset the time by setting the `tstart` value to 0. You can also change the starting iteration `iter_start` to reset the iteration counter. 

This last option is to be manipulated with precaution. You might end up overwriting existing snapshots if you this carelessly.

## A note on the `main` file

Because Dyablo has no way to know if your restart was meant to extend a run, start from scratch, or recover from a crash, the `main.xmf` file is written form scratch. Which means you will not have the info of the previous run in it. You can recondtruct manually or with a small python script the full list of files if necessary.


## Final word

Congratulations, you have finished the user tutorial. You are now ready to run Dyablo and play with configuration files. Feel free to experiment with the available runs in the `settings` folder located at the root of Dyablo ! 