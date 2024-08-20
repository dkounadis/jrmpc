# jrmpc
3D Point Cloud Alignment


This software ports the matlab code of the JRMPC algorithm from [inria.fr](https://team.inria.fr/perception/research/jrmpc/) from

```
Georgios Evangelidis, Dionyssos Kounades-Bastian, Radu Horaud, Emmanouil Psarakis. 
"A Generative Model for the Joint Registration of Multiple Point Sets."
European Conference on Computer Vision, Sep 2014, Zurich, Switzerland. 
pp.109-122, ⟨10.1007/978-3-319-10584-0_8⟩. ⟨hal-01019661v3⟩


Georgios Evangelidis, Radu Horaud. 
"Joint Alignment of Multiple Point Sets with Batch and Incremental Expectation-Maximization."
IEEE Transactions on Pattern Analysis and Machine Intelligence, 2018, 
40 (6), pp.1397 - 1410. ⟨10.1109/TPAMI.2017.2717829⟩. ⟨hal-01413414⟩
```

## MATLAB

Two demos are also provided that use the above algorithm to jointly register multiple views.

### Artificial Bunny

```
demoJrmpSynthetic.m
```

reads the views (point clouds) from `./sytheticData/` then
calls JRMPC algorithm to do the registration and finally visualizes the final alignment
and also measures convergence in terms of the ground rotation. 
`./sytheticData/` contains partial views and 360-views of Stanford 3D model 
"Bunny", after adding noise and outliers. Note also that each set is downsampled 
by its own factor. The case of partial views is similar to what has been used in 
the paper. 

```
Filenames of partial views: cutView1.txt, cutView2.txt ...
Filenames of 360-views: View1.txt View2.txt ...
```

### TimeOfFlight

```
demoJrmpcTof.m
``` 

reads the point sets (views) from ./tofData/, calls JRMPC to do the registration
and then visualizes the final alignment. ./tofData/ contains data acquired using a TOF camera,
the files have also color information in the last 3 columns, acquired using a stereo pair of
still cameras calibrated with the TOF. Note that color information is not used for registration, 
while it is not very accurate close to occlusions. We suggest using color information
only for visualization.

### Notes

The function removePointsAndFlatCenters is a helper function used by the above demos
It takes the output of JRMPC and tries to "detect" outliers in X and V, as described in the
paper.

```
For documentation on removePointsAndFlatCenters type in the matlab console:

    >> help removePointsAndFlatCenters

For documentation on jrmpc, type in matlab console:

    >> jrmpc

or simply call it with no arguments:

    >> jrmpc()
```    


Note that:

1) Sometimes, we initialize the cluster centers with random points (points on a sphere surface) 
and we scale them to have the same order with data points. However, you may want 
to initialise the cluster centers by subsampling one of the point sets, 
or by subsampling all the registered point-sets if they are already roughly registered. 

2) If you have a good initialization for the point centers, 
you may want to keep it fixed for some iterations in the beginning (just comment out the 
lines that update variable X) until variances converge a little, 
and then let both be updated. 

3) You may want to tune the parameters based on your datasets and/or include any prior knowledge
through your initialisation. 
If do not have any prior knowledge you can play with the order of variance ’S’ (.1, 1, 10,100,..) 
and/or the value of ‘gamma’ (try also zero value for gamma). 
You may also want to subsample your data to reduce the complexity. 
Most likely, a reasonable subsampling does not affect the accuracy of rotations/translations.

4) The visualisation of the registered sets in case of TOF data is very difficult and slow in Matlab. 
We strongly recommend to use the provided function mat2off.m and save the 
registered point set to OFF files. Then use an compatible software to display the OFF file.