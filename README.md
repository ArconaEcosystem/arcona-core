# Arcona Core
Download release [here](https://github.com/ArconaEcosystem/arcona-core/releases).

This library presents solutions in the fields of prototyping and computer vision, that have been developed instead of the corresponding existing standard ones.

Please send us any of your requests, recommendations and complaints to feedback@arcona.io.


PACKAGE CONTENTS

The package consists of the following files:
1. EULA and manual (this one);
2. ArconaCore_100.h – the header library file.
3. ArconaCore_100.lib,  ArconaCore_100d.lib - the compiled library files in the release and debug configuration respectivey.




OVERVIEW

This library presents solutions in the fields of prototyping and computer vision, that have been developed instead of the corresponding existing standard ones. Of course, the presented solutions aren’t the best for any purpose, but in several particular important tasks of the Arcona project they work better than the corresponding ones proposed by such famous libraries like PCloud, OpenCV and so on. Advantage of the presented methods is provided by using elements of neural network technologies, as particular, multi-layer convolutional neural networks. For user convenience the library introduces a number of input / output data types written in a simple style as well.




TECHNICAL INFORMATION

The Arcona Core is a statically–linked C++ library that is intended to use in Microsoft Visual C++ (c) environment of version 2013 or higher. The library is assembled with all necessary dependencies, so no extra procedures are needed to begin use it. The current version works on 64x platforms only. The hardware system requirements depend on the dimension and complexity of a specified input data, but for a majority of cases a platform with Intel I5 and 4 GB of memory is sufficient.    

All hi-level operational functions of the library return 0 if the processing has been done without critical errors and a non-zero (currently 1) otherwise. They don’t throw exceptions to the external code.   




PROTOTYPING SECTION

Input/Output Data Types

VECTOR – represents a point (vector) in 3D;

TRIANGLE – represents a triangle in a 3D mesh, each triangle vertex is defined by its index in the corresponding mesh point array; 

POINT_CLOUD  - represents a set of points: Points addresses the point array; NumPoints defines the number of the array points. Normasls contains the array of normal vectors at the correspoinding points of array Points; thus, its size should be equal to NumPoints as well or, if the point normals aren't specified, Normals should be initialized by NULL.   

MESH - represents a triangle mesh, is derived from POINT_CLOUD and supplemented by the fields, that represents the mesh triangles: Triangles addresses the triangle array; NumTriangles defines the number of the array triangles.

For the both structures there are the corresponding deallocation functions: delete_point_cloud and delete_mesh respectively.



Operational Methods

int make_huge_mesh(const POINT_CLOUD& point_cloud, MESH& out_mesh) – makes a triangle mesh from a specified cloud of sampled points. The function is intended to process big point clouds (1M points or greater), when many of existing solutions don’t work property. The function estimates the input cloud and chose the most suitable mesh creation method.  The output mesh structure should not be allocated, its allocation is made inside the function. 


int repair_mesh(const MESH& in_mesh, int flags, MESH& out_mesh) – repairs a specified incomplete or damaged mesh. As a particular, it fills missing surface regions, heals self-intersections and degenerated triangles.

The repairing process is managed by the following flags:
BasRelief - tells the function that the input is an unclosed bas-relief (for example, a terrain scan). Ignoring this option for such samples essentially increases the processing time and leads to improper results, because by default the function tries to make a manifold.
NoSelfIntersections - specifies repairing in a mode that prevents possible self-intersections in the output mesh. The duration in this case can be sufficiently longer; at the same time, processing without this option allows to obtain the output without self-intersections in a majority of cases.
TunnelLikeHolesExpected - indicates that the input can contain tunnel-like holes whose internal parts are lost (topologically similar to the hole in a torus). Detecting such holes decreases the performance and is recommended only if such holes really can take place.

The output mesh structure should not be allocated.

The repairing is performed by several convolutional recursive layers. It provides very good repairing abilities with comparison with currently existing methods. At the same time, convolutional operations in 3D layers are costly enough, so for big and seriously damaged models the repairing duration can exceed 10 min. The convolutional network is configured and adjusted automatically, so no more information than the described above flags is needed.    




COMPUTER VISION SECTION

Input/Output Data Types

POINT – represents a point in an image coordinate system;

POLYLINE – represents a polyline with assumption that there is an array of points that contains all the polyline vertices which are places consequently in it; OriginPointIndex addressed the beginning polyline vertex; NumPoints defines the total number of vertices.

POLYLINES represents a polyline set; Points and NumPoints define the total vertex array; PolyLines and NumPolylines represent the array of polyline descriptors. For this structure there is the corresponding deallocation function delete_polylines.



Operational Methods

int detect_image_lines(const char* image_filename, const LINE_DETECTION_CONTEXT & context, POLYLINES& out_polylines) - searches lines in a specified image.

The searching at the beginning step is performed by two layer network that currently have a semi – automatic adjustment. Exactly, for each layer only two basic parameters should be specified via LINE_DETECTION_CONTEXT data structure. Each parameter should be in range (0, 1), for many cases the parameters can be initialized by the following values:
Layer1Context.ResponseThreshold = 0.13f;;
Layer1Context.DecisionThreshold = 0.13f;
Layer2Context.ResponseThreshold = 0.8f;
Layer2Context.DecisionThreshold = 0.8f;          
The context structure contains the threshold parameter for the length of a detected line (LengthThreshold), lines shorter than this threshold are ignored to avoid image noise influence.

The found image lines are returned via the last parameters, the structure should not be allocated.
