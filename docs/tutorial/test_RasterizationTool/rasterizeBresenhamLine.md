# **rasterizeBresenhamLine (2D & 3D)**


These functions implement Bresenham’s algorithm to generate discrete grid points that approximate a line segment in 2D or 3D space.
Each function returns a unique set of integer coordinates stored in a `RowVector2iSet` or `RowVector3iSet`.

## **Function Signatures**

### **2D Version**
```cpp
RowVector2iSet rasterizeBresenhamLine2D(
    const Eigen::RowVector2d& p1,
    const Eigen::RowVector2d& p2
);

RowVector2iSet rasterizeBresenhamLine2D(
    const Eigen::RowVector2d& p1,
    const Eigen::RowVector2d& p2,
    std::vector<Eigen::RowVector2i>& rawLinePoints
);
```
- **Parameters**: 
    - `p1`, `p2` (`Eigen::RowVector2d`): Endpoints of the line segment in 2D space.  
    - `rawLinePoints` (optional, `std::vector<Eigen::RowVector2i>`): Sequence of discrete grid points along the line before corner expansion.

- **Returns**: A `RowVector2iSet` containing the line points along with their four neighboring corner offsets.

---

### **3D Version**
```cpp
RowVector3iSet rasterizeBresenhamLine3D(
    const Eigen::RowVector3d& p1,
    const Eigen::RowVector3d& p2
);

RowVector3iSet rasterizeBresenhamLine3D(
    const Eigen::RowVector3d& p1,
    const Eigen::RowVector3d& p2,
    std::vector<Eigen::RowVector3i>& rawLinePoints
);
```
- **Parameters**: 
    - `p1`, `p2` (`Eigen::RowVector3d`): Endpoints of the line segment in 3D space.  
    - `rawLinePoints` (optional, `std::vector<Eigen::RowVector3i>`): Sequence of discrete voxels along the line before corner expansion.

- **Returns**: A `RowVector3iSet` containing the line voxels along with their eight neighboring offsets.

---


## **Example Usage**

```cpp
#include "RasterizeTool.h"

int main() {

    Eigen::RowVector3d A(748674.4539194419, 2564739.5306861168, 49);
    Eigen::RowVector3d B(748694.42507040221, 2564734.3476669602, 81);
	RowVector3iSet linePointsSet = rasterizeBresenhamLine3D(A, B);
	
	return 0;
}
```

Below is a visualization of the rasterized 3D line segment between points A and B:

<a id="close-lightbox" style="display: none;"></a>
<div class="single-img-container">
    <a href="#lightbox-single" class="lightbox-trigger">
        <img src="../line/bresenham1.png" 
             alt="Bresenham 3D line rasterization result"> 
    </a>
</div>

<div id="lightbox-single" class="lightbox">
    <a href="#close-lightbox" class="lightbox-close">&times;</a>
    <img src="../line/bresenham1.png" alt="Bresenham 3D line rasterization result (Large)">
</div>