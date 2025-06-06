# **computePlaneNormal**

This document describes a function that calculates the unit normal vectors of a 3D plane defined by a set of coplanar points.

---

## **Function Signature**
```cpp
std::pair<Eigen::RowVector3d, Eigen::RowVector3d> computePlaneNormal(
    const Eigen::MatrixX3d& planePoints
);
```

---


## **Parameters**
- **planePoints**: Matrix of 3D points (`Eigen::MatrixX3d`), each row represents a point lying on the same plane. The matrix must contain at least three coplanar points.

---

## **Return Value**

- `std::pair<Eigen::RowVector3d, Eigen::RowVector3d>`:
  
    **first**: A unit normal vector perpendicular to the plane defined by `planePoints`.

    **second**: The opposite unit normal (the negation of the first).

---

## **Example Usage** 

```cpp
#include "LiteGeometry.h"

int main() {
    Eigen::Matrix<double,4,3> Vertices;
    Vertices << 748694.4250704022, 2564734.3476669602, 49.5,
        748674.4539194419, 2564739.5306861168, 81.5,
        748674.4154279609, 2564734.5007915981, 49.5,
        748694.4635618832, 2564739.3775614789, 81.5;

    const auto& [v1, v2] = computePlaneNormal(Vertices);
    std::cout << "[computePlaneNormal] Result:\n";
    std::cout << "  Normal Vector 1 (v1): " << v1.format(Eigen::FullPrecision) << "\n";
    std::cout << "  Normal Vector 2 (v2): " << v2.format(Eigen::FullPrecision) << "\n\n";

    return 0;
}
```
Visualization of the computed plane normals:

<a id="close-lightbox" style="display: none;"></a>
<div class="single-img-container">
    <a href="#lightbox-single" class="lightbox-trigger">
        <img src="../fig/planeNormal.png" 
             alt="Plane normal visualization"> 
    </a>
</div>

<div id="lightbox-single" class="lightbox">
    <a href="#close-lightbox" class="lightbox-close">&times;</a>
    <img src="../fig/planeNormal.png" alt="Plane normal visualization (Large)">
</div>