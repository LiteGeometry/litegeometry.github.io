# **isPointInPolygon (2D & 3D)**
These functions determine whether a point lies inside or on the boundary of a polygon in 2D or 3D space.


---

## **Function Signatures**

### **2D Version**

```cpp
bool isPointInPolygon2D(
    const Eigen::MatrixX2d& polygonVertices,
    const Eigen::RowVector2d& point,
    bool checkBoundary = true,
    bool needClosePolygon = true
);
```

### **3D Version**

```cpp
bool isPointInPolygon3D(
    const Eigen::MatrixX3d& polygonVertices,
    const Eigen::RowVector3d& point,
    bool checkBoundary = true,
    bool needClosePolygon = true
);
```

---

## **Parameters**

| Parameter          | Type                                   | Description                                                                                                 |
|--------------------|----------------------------------------|----------------------------------------------|
| `polygonVertices`  | `Eigen::MatrixX2d` / `Eigen::MatrixX3d`| Each row is a vertex. If `needClosePolygon` is `true`, the vertices may be unordered or open; the function will sort them counter-clockwise and append the first vertex to close the loop.                   |
| `point`            | `Eigen::RowVector2d` / `Eigen::RowVector3d` | Point to test.                                                    |
| `checkBoundary`    | `bool` (default = `true`)     | `true` → points on edges/vertices count as inside; `false` → only strictly interior points count.     |
| `needClosePolygon` | `bool` (default = `true`)     | `true` → auto-order & close polygonVertices; `false` → assume vertices are already counter-clockwise and closed.   |


---

## **Return Value**

- `true` if the point lies **inside** or **on the boundary** (when `checkBoundary=true`).
- `false` if the point lies **outside**, or if the polygon is degenerate/invalid.


---

## **Notes**

- The polygon must be **simple** (no self-intersections) and **planar**.
- When `needClosePolygon = true`, the function will:
    1. Sort vertices counter-clockwise.
    2. Append the first vertex to the end to close the loop.

    Disable automatic closing (`needClosePolygon = false`) when reusing an already-processed polygon for better performance.

---


## **Example Usage**

```cpp
#include "lightGeo.h"

int main() {
    // Unordered, unclosed 3D polygon vertices
    Eigen::Matrix<double,4,3> unorderedVertices;
    unorderedVertices << 748694.4250704022, 2564734.3476669602, 49.5,
        748674.4539194419, 2564739.5306861168, 81.5,
        748674.4154279609, 2564734.5007915981, 49.5,
        748694.4635618832, 2564739.3775614789, 81.5;
        
    Eigen::RowVector3d testPoint(748674.435, 2564737.016, 65.5);	

    // Test point located on one edge of the polygon
    bool isInsideWithEdge = isPointInPolygon3D(unorderedVertices, testPoint);

    // Strict: boundary does NOT count
    bool isInsideStrict = isPointInPolygon3D(unorderedVertices, testPoint, /*checkBoundary=*/false);


    std::cout << "Including boundary: " << insideWithEdge << "\n";
    std::cout << "Strict interior only: " << strictInside << "\n";
    
    return 0;
}
```

This example evaluates a point that lies on the polygon edge.

*Left*: with `checkBoundary = true`, the point is considered inside (`true`).

*Right*: with `checkBoundary = false`, the same point is considered outside (`false`).

<a id="close-lightbox" style="display: none;"></a>
<div class="single-img-container">
    <a href="#lightbox-single" class="lightbox-trigger">
        <img src="../fig/isPointInPolygon.png" 
             alt="Point-in-polygon visualization"> 
    </a>
</div>

<div id="lightbox-single" class="lightbox">
    <a href="#close-lightbox" class="lightbox-close">&times;</a>
    <img src="../fig/isPointInPolygon.png" alt="Point-in-polygon visualization (Large)">
</div>