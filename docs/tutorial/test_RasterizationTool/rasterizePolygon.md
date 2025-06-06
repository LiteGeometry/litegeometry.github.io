# **rasterizePolygon (2D & 3D)**


These functions generate discrete grid points that approximate the boundary and interior of a polygon in 2D or 3D space.
Each function returns a unique set of integer coordinates stored in a `RowVector2iSet` or `RowVector3iSet`. Neighboring offset points are also included.

---


## **Function Signatures**

### **2D Version**
```cpp
RowVector2iSet rasterizePolygon2D(
    const Eigen::MatrixX2d& polygonVertices,
    bool needClosePolygon = true
);
```

- **Parameters**: 
    - `polygonVertices` (`Eigen::MatrixX2d`): Vertices of the 2D polygon.  
    - `needClosePolygon` (optional, `bool`, default = `true`): 
        - If `true`, the function automatically sorts the vertices counter-clockwise and ensures the polygon is closed.
        - If `false`, the input vertices matrix must already be sorted and closed.

- **Returns**: A `RowVector2iSet` containing all boundary and interior grid points, plus their four corner offsets.

---

### **3D Version**
```cpp
RowVector3iSet rasterizePolygon3D(
    const Eigen::MatrixX3d& polygonVertices,
    bool needClosePolygon = true
);
```

- **Parameters**: 
    - `polygonVertices` (`Eigen::MatrixX3d`): Vertices of the 3D polygon.  
    - `needClosePolygon` (optional, `bool`, default = `true`): 
        - If `true`, the function automatically sorts the vertices counter-clockwise and ensures the polygon is closed.
        - If `false`, the input vertices matrix must already be sorted and closed.

- **Returns**: A `RowVector3iSet` containing all boundary and interior voxels, plus their eight surrounding offsets.

---

## **Example Usage**
```cpp
#include "RasterizeTool.h"

int main() {

    Eigen::Matrix<double, 4, 3> polygonVertices;
    polygonVertices << 748694.42507040221, 2564734.3476669602, 49,
           748674.41542796092, 2564734.5007915981, 49,
           748674.4539194419, 2564739.5306861168, 81,
           748694.4635618832, 2564739.3775614789, 81;
  
    RowVector3iSet PointsSet = rasterizePolygon3D(polygonVertices);
    
    return 0;
}
```
The figure below shows the result of rasterizing a 3D polygon:

<a id="close-lightbox" style="display: none;"></a>
<div class="single-img-container">
    <a href="#lightbox-single" class="lightbox-trigger">
        <img src="../polygon/bresenham2.png" 
             alt="3D polygon rasterization result"> 
    </a>
</div>

<div id="lightbox-single" class="lightbox">
    <a href="#close-lightbox" class="lightbox-close">&times;</a>
    <img src="../polygon/bresenham2.png" alt="Bresenham 3D line rasterization result (Large)">
</div>