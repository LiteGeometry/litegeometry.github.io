# **isLinesIntersection (2D & 3D)**

Functions that test whether two line primitives (segments, rays, or infinite lines) intersect in 2D or 3D space.

---

## **Function Signatures**

### **2D Intersection**

```cpp
template<typename LineT>
// Check intersection and optionally retrieve the intersection point.
bool isLinesIntersection2D(
    const LineT& geoAB,
    const LineT& geoCD,
    Eigen::RowVector2d& intersection
);

// Overload without retrieving the intersection point. 
template<typename LineT>
bool isLinesIntersection2D(
    const LineT& geoAB,
    const LineT& geoCD
);
```
#### **Parameters**

- **geoAB**: The first 2D line primitive (e.g., `Segment2D`, `Ray2D`, `Line2D`). Must provide members `P1` and `P2` of type `Eigen::RowVector2d`.

- **geoCD**: The second 2D line primitive, with the same requirements as `geoAB`.

- **intersection**: (*Optional*) If the line primitives intersect, receives the intersection point.


#### **Return Value**
- `true` if the two line primitives intersect; otherwise, `false`.


### **3D Intersection**

```cpp
// Check intersection within a distance threshold and retrieve the closest points.
template<typename T1, typename T2>
bool isLinesIntersection3D(
    const T1& geoAB,
    const T2& geoCD,
    std::tuple<Eigen::RowVector3d, Eigen::RowVector3d, double>& output,
    double threshold
);

// Overload without retrieving the intersection point.
template<typename T1, typename T2>
bool isLinesIntersection3D(
    const T1& geoAB,
    const T2& geoCD,
    double threshold
);
```

#### **Parameters (3D Version)**

| Parameter         | Type                               | Description                                                                                                 |
|-------------------|------------------------------------|-------------------------------------------------------------------------------------------------------------|
| `geoAB`           | Any 3D line primitive (e.g., `Segment3D`, `Ray3D`, `Line3D`) with `P1`, `P2` of type `Eigen::RowVector3d`.              | First line/ray/segment. |
| `geoCD`           | Same as `geoAB`.              | Second line/ray/segment. |
| `output`          | `std::tuple<Eigen::RowVector3d, Eigen::RowVector3d, double>` (*optional*) | If the line primitives intersect, this parameter will hold the closest points and the distance.           |
| `threshold`       | `double`                           | The maximum distance for which the line primitives are considered to intersect (only for 3D versions).    |


#### **Return Value**
- `true` if the minimum distance between the two line primitives is ≤ `threshold`; otherwise `false`.

---

## **Example Usage**

```cpp
#include "LiteGeometry.h"

int main() {
    // Define the endpoints of two 3D segments
    Eigen::RowVector3d A(1, 1, 0);
    Eigen::RowVector3d B(0, -1, 0.4);
    Eigen::RowVector3d C(1, -1, 0);
    Eigen::RowVector3d D(0, 0, 0.5);
    Segment3D AB{ A ,B };
    Segment3D CD{ C ,D };

    // Tuple to hold the closest points and their distance
    std::tuple<Eigen::RowVector3d, Eigen::RowVector3d, double> intersectionInfo;

    // Test 1: threshold = 0.01
    bool intersects1 = isLinesIntersection3D(AB, CD, intersectionInfo, 0.01);

    // Test 2: threshold = 0.1
    bool intersects2 = isLinesIntersection3D(AB, CD, intersectionInfo, 0.1);
    
    return 0;
}
```

The minimum distance between segments **AB** and **CD** is 0.0604.

*Left*: with `threshold = 0.01`, the segments are considered non-intersecting.

*Right*: with `threshold = 0.1`, they are considered intersecting.

<a id="close-lightbox" style="display: none;"></a>
<div class="single-img-container">
    <a href="#lightbox-single" class="lightbox-trigger">
        <img src="../fig/isLinesIntersection.png" 
             alt="Visualization of two linear primitives intersecting in 3D space."> 
    </a>
</div>

<div id="lightbox-single" class="lightbox">
    <a href="#close-lightbox" class="lightbox-close">&times;</a>
    <img src="../fig/isLinesIntersection.png" alt="Visualization of two linear primitives intersecting in 3D space. (Large)">
</div>