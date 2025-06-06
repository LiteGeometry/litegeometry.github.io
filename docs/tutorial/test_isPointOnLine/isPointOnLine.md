# **isPointOnLine (2D & 3D)**
Determine whether a query point lies on a specified line primitive in 2D or 3D space.

Supported types include:

- 2D: `Segment2D`, `Ray2D`, or `Line2D`
- 3D: `Segment3D`, `Ray3D`, or `Line3D`

## **Overview**
A query point **P** is considered on the primitive **AB** when both conditions hold:

| Criterion | Meaning |
|-----------|---------------------|
| **Collinearity** | **P**, **A**, and **B** are collinear (within numerical tolerance).  |
| **Parametric bounds** | **P** falls within the parameter range that defines the primitive:<br> &nbsp;&nbsp;• **Segment** – between (or at) the endpoints.<br> &nbsp;&nbsp;• **Ray** – on or beyond the origin, in the ray’s direction.<br> &nbsp;&nbsp;• **Line** – unbounded; any collinear point qualifies.  |


---

## **2D Version**
### **Function Signatures**
```cpp
template<typename LineT>
bool isPointOnLine2D(
    const LineT& geoAB,
    const Eigen::RowVector2d& point
);
```

### **Parameters**
- `geoAB` (`LineT`): 2D line primitive (`Segment2D`, `Ray2D`, `Line2D`).
- `point` (`Eigen::RowVector2d`): The point to be tested.

### **Return Value**
- `true`: `point` satisfies both **Collinearity** and **Parametric bounds**.

- `false`: Otherwise.

---

## **3D Version**
### **Function Signatures**
```cpp
template<typename LineT>
bool isPointOnLine3D(
    const LineT& geoAB,
    const Eigen::RowVector3d& point
);
```

### **Parameters**
- `geoAB` (`LineT`): 3D line primitive (`Segment3D`, `Ray3D`, `Line3D`).
- `point` (`Eigen::RowVector3d`): The point to be tested.


### **Return Value**
Same as the 2D version.

---


## **Example Usage**

```cpp
#include "LiteGeometry.h"
int main() {

    Eigen::RowVector3d A(0, 0, 0);
    Eigen::RowVector3d B(1, 1, 1);
    Eigen::RowVector3d C(0.5, 0.5, 0.5);
    Eigen::RowVector3d D(2, 2, 2);
    Eigen::RowVector3d E(-1, -1, -1);
    Segment3D segAB = Segment3D{ A,B };
    Ray3D rayAB = Ray3D{ A,B };
    Line3D lineAB = Line3D{ A,B };

    std::cout << "--- On Segment AB ---\n";
    std::cout << "  C on Segment AB: " << isPointOnLine3D(segAB, C) << "\n";
    std::cout << "  D on Segment AB: " << isPointOnLine3D(segAB, D) << "\n";
    std::cout << "  E on Segment AB: " << isPointOnLine3D(segAB, E) << "\n\n";

    std::cout << "--- On Ray AB ---\n";
    std::cout << "  C on Ray AB: " << isPointOnLine3D(rayAB, C) << "\n";
    std::cout << "  D on Ray AB: " << isPointOnLine3D(rayAB, D) << "\n";
    std::cout << "  E on Ray AB: " << isPointOnLine3D(rayAB, E) << "\n\n";

    std::cout << "--- On Line AB ---\n";
    std::cout << "  C on Line AB: " << isPointOnLine3D(lineAB, C) << "\n";
    std::cout << "  D on Line AB: " << isPointOnLine3D(lineAB, D) << "\n";
    std::cout << "  E on Line AB: " << isPointOnLine3D(lineAB, E) << "\n";
    
    return 0;
}
```


The figure below visualises the test results for points C, D, and E with respect to the segment, the ray, and the infinite line defined by A and B.
<a id="close-lightbox" style="display: none;"></a>
<div class="single-img-container">
    <a href="#lightbox-single" class="lightbox-trigger">
        <img src="../fig/isPointOnLine3D.png" 
             alt="Point-on-line visualization"> 
    </a>
</div>

<div id="lightbox-single" class="lightbox">
    <a href="#close-lightbox" class="lightbox-close">&times;</a>
    <img src="../fig/isPointOnLine3D.png" alt="Point-on-line visualization (Large)">
</div>