# **isCoplanar**

Checks whether a set of 3D points lies on the same plane.


---

## **Function Signatures**

```cpp
bool isCoplanar(const Eigen::MatrixX3d& points);
```

---
## **Parameters**
- **points**: Matrix of 3D points (`Eigen::MatrixX3d`, one point per row).


---

## **Return Value**
- `true` if all points are coplanar; otherwise, `false`.

---


## **Example Usage**
```cpp
#include "LiteGeometry.h"

int main() {
    Eigen::Matrix<double, 5, 3> points1;
    points1 << 
        0.0,   0.0,   0.5,
        0.0,   1.0,   0.5,
        1.0,   1.0,   0.5,
        1.0,   0.0,   0.5,
        0.5,   0.5,   1.0;

    bool isCoplanar1  = isCoplanar(points1);
    std::cout << "Test Case 1 (points1): ";
    if (isCoplanar1) {
        std::cout << "coplanar" << std::endl;
    } else {
        std::cout << "not coplanar" << std::endl;
    }

    Eigen::Matrix<double, 5, 3> points2;
    points2 << 
        0.0,   0.0,   0.5,
        0.0,   1.0,   0.5,
        1.0,   1.0,   0.5,
        1.0,   0.0,   0.5,
        0.5,   0.5,   0.5;

    bool isCoplanar2 = isCoplanar(points2);
    std::cout << "Test Case 2 (points2): ";
    if (isCoplanar2) {
        std::cout << "coplanar" << std::endl;
    } else {
        std::cout << "not coplanar" << std::endl;
    }

    return 0;
}
```

The following illustration shows the two test cases: 

- *left*: a non-coplanar set of points
- *right*: a coplanar configuration.

<a id="close-lightbox" style="display: none;"></a>
<div class="single-img-container">
    <a href="#lightbox-single-1" class="lightbox-trigger">
        <img src="../fig/coplanar.png" 
             alt="Coplanarity Example"> 
    </a>
</div>

<div id="lightbox-single-1" class="lightbox">
    <a href="#close-lightbox" class="lightbox-close">&times;</a>
    <img src="../fig/coplanar.png" alt="Coplanarity Example (Large)">
</div>
