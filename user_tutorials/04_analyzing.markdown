---
layout: page
title: Analyzing a run
permalink: /user_tutorials/analyzing
parent: User tutorials
nav_order: 4
---

# Analyzing a run

To analyze a run you have two default options: either use Paraview or pyablo.

## Paraview

Paraview is a very popular scientific visualization software. You can download it for free here : https://www.paraview.org/

By default, Dyablo outputs analysis snapshots with the format `.xmf`/`.h5`. You can open these files using paraview. If you look at the files created by Dyablo, you will see a list of outputs with the name `test_blast_3D_iterXXXXXXX.xmf` and `test_blast_3D_iterXXXXXXX.h5`. The value after `iter` is the iteration at which the output was saved. The `xmf` file stores the general structure of the snapshot while the `h5` file holds the geometrical and physical data. The `xmf` files can be directly opened in Paraview.

Alternatively, there is a `test_blast_3D_main.xmf` file that holds the whole time-series. You can load that file to see have the whole set of snapshots ordered in time. 

We refer you to the [Self directed paraview tutorial](https://docs.paraview.org/en/latest/Tutorials/SelfDirectedTutorial/index.html) for more information. For now, load the `main` file, then click on the green `Apply` button on the pipeline window on the left. You should now have in the center view a cube: 

![Initial Cube](/imgs/01_cube.png)

This cube represents our domain, and as you can notice in the top toolbar, the current view displays the total energy ("e_tot"). Click and drag the left button of your mouse in the center view to rotate the cube to a more comfortable position.

![Rotated Cube](/imgs/02_cube.png)

Now click the `clip` button in the toolbar ![clip button](/imgs/03_clip.png) to create a clip in the pipeline. You should see the clipping plane appear on the center view. On the left, you can see now that there is a clipping plane after your file in the pipeline browser and below in the properties inspector, you should see the properties of the clipping plane. Simple click `Apply` for now. Your cube should now be cut in half and you should see the initial conditions of the cube: 

![Rotated cube with clipping](/imgs/04_clipped_cube.png).

In the properties inspector, uncheck the `Show plane` button to remove the invasive display in the center. For that run Dyablo has only written two snapshots, one of the initial conditions which you are seeing now, and one of the final state at the end of the run. To move between these snapshots you can use the controls in the toolbar : 

![Toolbar snapshot controls](/imgs/05_toolbar.png).

Click the right arrow and move to the last snapshot. You should see the final state of the simulation for the total energy variable:

![Rotate cube with clipping, final state](/imgs/06_clipped_cube.png)

Finally, let's inspect the mesh. You can change the way the data is displayed by playing with the representation menu in the toolbar: 

![Representation menu](/imgs/07_representation_menu.png)

Select "Surface with Edges" to display the data with the AMR edges highlighted. You should see that the blocks are refined around the blast and coarse around the corners of the box: 

![AMR cube of the data](/imgs/08_amr_cube.png)

There is a lot you can do with Paraview but learning to use and exploit the data is out of the scope of this tutorial. For more information, please check the [self directed tutorial](https://docs.paraview.org/en/latest/Tutorials/SelfDirectedTutorial/index.html). 


## Pyablo

Pyablo is an analysis package developed for reading Dyablo data. It is made to read and post-process small subsets of data in Python. To get and install Pyablo, we refer you to the official [github repository](https://github.com/Dyablo-HPC/Pyablo). Follow the instructions to download and compile the code. Once pyablo is compiled and added to your `$PYTHONPATH` environment variable you can start using it. Let's try to load and display the data: 

```python3
import pyablo
import numpy as np
import matplotlib.pyplot as plt

## Creating a reader
reader = pyablo.XdmfReader()

## Reading the time series
series = reader.readTimeSeries('test_blast_3D_block_main.xmf')
print('Available files in time series :', series)

## Reading the last snapshot
snap = reader.readSnapshot(series[-1])

## NGP projection on a regular grid
Nx = 64
Ny = 64
Nz = 64
extent = ((0.0, 0.0, 0.0), (1.0, 1.0, 1.0))
cid = snap.getCellIndicesForRegularGrid(extent, Nx, Ny, Nz)

## Helper lambda function to read data and transform it to a well-shaped
## numpy array
read_array = lambda arr_name: np.array(snap.getQuantity(cid, arr_name)).reshape((Nz, Ny, Nx))

## Reading density and total energy at these positions
rho = read_array('rho')
e_tot = read_array('e_tot')

## Plotting the data
fig, ax = plt.subplots(1, 2, figsize=(10, 5))
ax[0].imshow(rho[:,:,Nz//2], origin='lower', cmap='magma')
ax[1].imshow(e_tot[:,:,Nz//2], origin='lower', cmap='plasma')

ax[0].set_xlabel('x')
ax[0].set_ylabel('y')
ax[1].set_xlabel('x')
ax[0].set_title('Density')
ax[1].set_title('Total energy')

plt.show()
```

The code should be rather self explanatory:

1. We create a reader for the xmf/h5 files in Dyablo
2. We read the time series using `readTimeSeries`, this returns a list of filenames 
3. We read the last snapshot using `readSnapshot`
4. We get a list of indices to project on a regular grid with `getCellIndicesForRegularGrid`
5. We read the density and the total energy to numpy arrays
6. We plot a slice at the middle of the z-axis

The result should be very close to this: 

![The blast, sliced and rendered in Python](/imgs/09_pyablo.png)

There are more features included in pyablo. You can read more about it in the [readme of pyablo](https://github.com/Dyablo-HPC/Pyablo)