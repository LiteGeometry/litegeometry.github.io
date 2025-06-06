# **isLinePolyhedronIntersection**

Determine whether a 3D line primitive (segment, ray, or infinite line) intersects a polyhedron.

---

## **Function Signatures**
```cpp
// Test intersection and collect all intersection points.
template<typename LineT>
bool isLinePolyhedronIntersection(
    const Eigen::MatrixX3d& polyhedronVertices,
    const LineT& geoAB,
    std::vector<Eigen::RowVector3d>& intersections
)

// Overload without computing intersection coordinates.
template<typename LineT>
bool isLinePolyhedronIntersection(
    const Eigen::MatrixX3d& polyhedronVertices,
    const LineT& geoAB
)
```

---

## **Parameters**
- **polyhedronVertices** (`Eigen::MatrixX3d`): 
  Matrix of size `N×3`; each row represents a vertex of the polyhedron. Vertices may be unordered or unclosed.
- **geoAB** (`LineT`):
  The 3D line primitive (e.g., `Segment3D`, `Ray3D`, `Line3D`) to check for intersection with the polyhedron.
- **intersections** (`std::vector<Eigen::RowVector3d>&`):
  If provided, receives all computed intersection points..

---

## **Return Value**
  - `true` if there is at least one intersection; otherwise returns `false`.

---

## **Example Usage**

```cpp
#include "LiteGeometry.h"

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
    {
        Eigen::RowVector3d A(748674.6211, 2564712.4947, 64.5593);
        Eigen::RowVector3d B(748708.8839, 2564753.8769, 59.6075);
        Segment3D AB{ A ,B };
        std::vector<Eigen::RowVector3d> intersections;
        bool isIntersection = isLinePolyhedronIntersection(testPolyhedron, AB, intersections);
        std::cout << "\n-- Segment AB Intersection with Polyhedron --\n";
        if (isIntersection) {
            std::cout << "Intersection Points:\n";
            for (const auto& point : intersections) {
                std::cout << point.format(Eigen::FullPrecision) << "\n";
            }
        }
    }
    std::cout << "\n--------------------------------\n";
    {
        Eigen::RowVector3d A(748674.6211, 2564712.4947, 64.5593);
        Eigen::RowVector3d B(748680.9715683657, 2564720.1647197301, 63.641504336681471);
        Ray3D AB{ A, B };
        std::vector<Eigen::RowVector3d> intersections;
        std::cout << "\n-- Ray AB Intersection with Polyhedron --\n";
        bool isIntersection = isLinePolyhedronIntersection(testPolyhedron, AB, intersections);
        if (isIntersection) {
            std::cout << "Intersection Points:\n";
            for (const auto& point : intersections) {
                std::cout << point.format(Eigen::FullPrecision) << "\n";
            }
        }
    }
    
    return 0;
}
```

<a id="close-lightbox" style="display: none;"></a>
<div class="single-img-container">
    <a href="#lightbox-single" class="lightbox-trigger">
        <img src="../fig/isLinePolyhedronIntersection.png" 
             alt="Intersection of line primitives with a polyhedron"> 
    </a>
</div>

<div id="lightbox-single" class="lightbox">
    <a href="#close-lightbox" class="lightbox-close">&times;</a>
    <img src="../fig/isLinePolyhedronIntersection.png" alt="Intersection of line primitives with a polyhedron (Large)">
</div>