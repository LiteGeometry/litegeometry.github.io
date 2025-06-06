# **isPointInPolyhedron**

Determines whether a 3D point is located inside (or on the boundary of) a given polyhedron.

---

## **Function Signatures**

```cpp
bool isPointInPolyhedron(
    const Eigen::MatrixX3d& polyhedronVertices,
    const Eigen::RowVector3d& point,
    bool checkBoundary = true
);
```

---

## **Parameters**


| Parameter          | Type                                   | Description                                                                                                 |
|--------------------|----------------------------------------|----------------------------------------------|
| `polyhedronVertices`  | `Eigen::MatrixX3d`| A matrix of size `N×3`, each row representing a polyhedron vertex.                   |
| `point`            | `Eigen::RowVector3d` | The 3D point to test for containment in the polyhedron.                                                   |
| `checkBoundary`    | `bool` (default = `true`)     | If `true`, points lying on the polyhedron’s boundary (faces or edges) are considered inside.     |


---

## **Return Value**

- `true` if the point is inside the polyhedron, or on its boundary if `checkBoundary` is enabled.
- `false` if the point lies strictly outside the polyhedron.


---


## **Example Usage**

```cpp
#include "lightGeo.h"

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

    Eigen::RowVector3d testPoint1(20.785733063246152, 76.974749045767595, 55.56473456);
    Eigen::RowVector3d testPoint2(65.50742704979844, 77.06750108447973, 65.0984928161587);


    bool insideWithBoundary1 = isPointInPolyhedron(polyhedronVertices, testPoint1);
    bool insideStrict1   = isPointInPolyhedron(polyhedronVertices, testPoint1, false);

    bool insideWithBoundary2 = isPointInPolyhedron(polyhedronVertices, testPoint2);
    bool insideStrict2   = isPointInPolyhedron(polyhedronVertices, testPoint2, false);

    std::cout << "TestPoint1 (on face/edge):\n"
              << "  Including boundary: " << insideWithBoundary1 << "\n"
              << "  Strict only:       " << insideStrict1   << "\n\n";

    std::cout << "TestPoint2 (interior):\n"
              << "  Including boundary: " << insideWithBoundary2 << "\n"
              << "  Strict only:       " << insideStrict2   << "\n";
              
    return 0;
}
```

These examples demonstrate how the `checkBoundary` flag affects the classification of two test points:

- When `checkBoundary = false` (*left image*)，both points are considered outside the polyhedron
- When `checkBoundary = true` (*right image*)，both points are considered inside the polyhedron.

<a id="close-lightbox" style="display: none;"></a>
<div class="single-img-container">
    <a href="#lightbox-single" class="lightbox-trigger">
        <img src="../fig/isPointInPolyhedron.png" 
             alt="Point-in-polyhedron visualization"> 
    </a>
</div>

<div id="lightbox-single" class="lightbox">
    <a href="#close-lightbox" class="lightbox-close">&times;</a>
    <img src="../fig/isPointInPolyhedron.png" alt="Point-in-polyhedron visualization (Large)">
</div>