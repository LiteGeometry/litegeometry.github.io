# **rasterizePolyhedron**


This function generates discrete 3D grid points that approximate the surface of a polyhedron.

It returns a unique set of integer coordinates stored in a `RowVector3iSet`, including corner-based cell expansions around each voxel on the polyhedron’s surface.

---

## **Function Signatures**
```cpp
RowVector3iSet rasterizePolyhedron(
    const Eigen::MatrixX3d& polyhedronVertices
);
```
- **Parameters**: 
    - `polyhedronVertices` (`Eigen::MatrixX3d`): A matrix containing the 3D coordinates of the polyhedron's vertices.

- **Returns**: A `RowVector3iSet` that includes all surface voxels and their eight surrounding corner offsets.

---

## **Example Usage** 
```cpp
#include "RasterizeTool.h"

int main() {
	Eigen::Matrix<double, 8, 3> polyhedronVertices; 
    polyhedronVertices << 748674.4539194419, 2564739.5306861168, 49,
           748694.4635618832, 2564739.3775614789, 49,
           748694.42507040221, 2564734.3476669602, 49,
           748674.41542796092, 2564734.5007915981, 49,
           748674.4539194419, 2564739.5306861168, 81,
           748694.4635618832, 2564739.3775614789, 81,
           748694.42507040221, 2564734.3476669602, 81,
           748674.41542796092, 2564734.5007915981, 81;

	RowVector3iSet polyhedronPointsSet = rasterizePolyhedron(polyhedronVertices);
	
	return 0;
}
```

Below is a visualization of the rasterized polyhedron surface:
<a id="close-lightbox" style="display: none;"></a>
<div class="single-img-container">
    <a href="#lightbox-single" class="lightbox-trigger">
        <img src="../polyhedron/bresenham3.png" 
             alt="polyhedron rasterization result"> 
    </a>
</div>

<div id="lightbox-single" class="lightbox">
    <a href="#close-lightbox" class="lightbox-close">&times;</a>
    <img src="../polyhedron/bresenham3.png" alt="polyhedron rasterization result (Large)">
</div>