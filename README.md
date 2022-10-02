# space_carving

## Space Carving: Shape from Silhouettes

![carving](https://www.researchgate.net/profile/Bruno-Raffin/publication/40809954/figure/fig10/AS:325729033179155@1454671261615/Visual-hull-of-a-person-with-4-views_W640.jpg)

**Figure 1**: 3-D visual hull of a person reconstructed from four views (Source: https://www.researchgate.net/publication/40809954_Multi-Camera_Real-Time_3D_Modeling_for_Telepresence_and_Remote_Collaboration).

#### Assignment overview

In this assignment, you will use *space carving* to reconstruct the 3-D shape of the persons moving in a scene. The persons' shape will be reconstructed from a set of images acquired with multiple synchronized video cameras. The cameras are fully calibrated. The assignment's deliverable will be a report with the source code of the method and a video of the 3-D reconstruction. 

#### Shape from silhouettes

This type of problem is a direct application of the pinhole camera model when using multiple synchronized calibrated cameras. In this multi-view setup, a 3-D world point ${\bf w}$ projects onto an image point ${{\bf x}}_{j}$ on the $j^{th}$ camera. The pinhole-camera model for the  $j^{th}$ camera is: 
```
\begin{align}
    \lambda \tilde{{\bf x}}_{j} = 
    \Lambda_j
    \begin{bmatrix}
        \Omega_j & {\boldsymbol{\tau}_j}
    \end{bmatrix} \tilde{{\bf w}}. 
\end{align}
```
In the basic space-carving algorithm, we usually want to reconstruct the foreground objects from a set of image silhouettes of the object. Figure 1 shows a set of silhouettes of a person seen by four different views. Figure 2 shows an example of segmentation using a method called *background subtraction*. 

<img src="https://opencv24-python-tutorials.readthedocs.io/en/latest/_images/resframe.jpg" alt="carving" style="zoom:150%;" />

<img src="https://opencv24-python-tutorials.readthedocs.io/en/latest/_images/resmog.jpg" alt="Result of BackgroundSubtractorMOG" style="zoom:150%;" />

**Figure 2**: Example of foreground segmentation using background subtraction (Source: https://opencv24-python-tutorials.readthedocs.io/en/latest/py_tutorials/py_video/py_bg_subtraction/py_bg_subtraction.html).

Another example of silhouettes seen from multiple views in show in Figure 3. The figure shows the (segmented) sillhouette of a toy bunny in multiple camera views. 

![bunny](bunny.png)

**Figure 3**: A toy bunny and its image silhouettes projects onto four cameras. 

The silhouettes work as binary masks which are obtained by segmenting the foreground object from its surrounding background.  The segmented foreground is converted to a binary mask in which pixels inside the object's silhouette are set to 1 and pixels outside are set to 0. Figure 4 illustrates the concept of a binary mask.

<img src="bunnyBinaryMask.png" alt="bunnyBinaryMask" style="zoom: 25%;" />

**Figure 4**: A toy bunny binary mask. Pixels inside the object's silhouette are set to 1, and pixels outside are set to 0.

Once the binary silhouettes are at hand, we can create a virtual volume (e.g., a dense sample 3-D points around the expected position of the 3-D object in space) and project these virtual points onto the images of all cameras. To visualize the virtual volume, we can project the 3-D virtual points to one or all of the camera views (Figures 5). Projecting the virtual volume onto the multiple camera views help us locate the virtual volume to ensure it encloses the target object. 

<img src="project_loop_01.png" alt="project_loop_01" style="zoom:50%;" />

<img src="project_loop_02.png" alt="project_loop_02" style="zoom:50%;" />

**Figure 5**: Projection of the virtual sampling volume onto two camera views. In this example, the virtual object appears to be in front of the person. Depending on the goals of the reconstruction, we might need to move the virtual cube in space to ensure it encloses the foreground object.  

Another (and simple) way to help position the virtual volume is by projecting its origin onto the silhouette images of all camera views (Figure 6). 

![projectOrigin](projectOrigin.png)

**Figure 6**: Projection of the origin of the virtual sampling volume onto 8 camera views. In this example, the origin of the virtual volume seems to be located in front of the person. Plotting also the vertices of the base of the virtual volume (i.e., one the ground plane) and linking them with lines also help with visualization of its position with respect to the target object. 

The reconstruction of the object's shape is done by labeling if a point in the virtual volume is one of two types, namely, an *object point* or a *non-object point*. A virtual-volume point is an *object point* if its projections lie inside the object's silhouettes on all images (Figure 7).

<img src="insidePoint.png" alt="insidePoint" style="zoom: 25%;" />

**Figure 7**: A virtual volume point is labeled an *object point* if and only if it ``hits'' the object's silhouettes in all camera views. 

A virtual point that lies outside in any of the silhouettes of the object is labeled as a non-object point.



<img src="outsidePoint.png" alt="outsidePoint" style="zoom:25%;" />

**Figure 8**: A virtual volume point is labeled a non-object point if it ``misses'' the object's silhouettes in any of the camera views. 

The final reconstructed points is the set of all object points, which can be plotted as a cloud of points or as a 3-D mesh. 

The basic algorithm is then: 

```matlab
% Create voxel volume, and set all values to zero (i.e., empty or non-object point)
V = zeros( nrows, ncols, depth ); 

% Loop over all voxels in volume V
for all voxels in V do
    
    % loop over all cameras
    for all cameras do
       
        % Calculate projection of the voxel on the camera
        x = P * X;
        
        % Is x inside silhouette? If YES then label voxel as full (i.e., object point). 
        
    end
    
end

% Display all voxels in V that have label == full. These voxels form a
% cloud of points. 

% Calculate the iso-surface on the cloud of points and display resulting
% meshed model. 
```



#### Deliverables

A brief report containing the following: 

- The code of your solution 
- A link to a video showing the reconstructed shapes as a 3-D plot animation. 

You should make an effort to produce the report in Jupyter notebook, with equations in LaTeX, and your own observations. 



#### Image/Video Dataset

You will use the camera setup and images from the Soldiers dataset (https://www.epfl.ch/labs/cvlab/data/soldiers-tracking/) 

#### Implementation suggestions

To create the silhouettes and binary masks, you can use the background-subtract functions from OpenCV: https://opencv24-python-tutorials.readthedocs.io/en/latest/py_tutorials/py_video/py_bg_subtraction/py_bg_subtraction.html



#### Readings

- Daniel Vlasic, Ilya Baran, Wojciech Matusik, Jovan Popovic: Articulated Mesh Animation from Multi-view Silhouettes. ACM Transactions on Graphics 27(3), 2008. 
- Ahmed, M.T.; Eid, A.H.; Farag, A.A.: 3-D reconstruction of the human jaw using space carving. Proceedings. International Conference on Image Processing, pp.323,326 vol.2, 2001. 
- Yasemin Kuzu and Volker Rodehorst. Volumetric modeling using shape from silhouette. Fourth Turkish-German Joint Geodetic Days, pp. 469–476, 2001. 

#### 
