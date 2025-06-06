# **WorldToCameraImageCoords**

This document introduces two overloaded functions that project 3D world points into 2D camera image coordinates using a pinhole camera model. 

---

## **Function Signatures**

 **1. Explicit Look‑At Projection**
```cpp
Eigen::MatrixX2d WorldToCameraImageCoords(
    const Eigen::MatrixX3d& targetVertices,
    const Eigen::RowVector3d& cameraDirPoint,
    const Eigen::RowVector3d& cameraPosition,
    double FL
);
```

 **2. Centroid‑Based Projection**
```cpp
Eigen::MatrixX2d WorldToCameraImageCoords(
    const Eigen::MatrixX3d& targetVertices,
    const Eigen::RowVector3d& cameraPosition,
    double FL
);
```
---

## **Parameters**
- **targetVertices**: `Eigen::MatrixX3d`, where each row is a 3D point to project.
- **cameraDirPoint**: `Eigen::RowVector3d`, specifying the point the camera looks at (only for overload 1).
- **cameraPosition**: `Eigen::RowVector3d`, representing the camera’s position in world coordinates.
- **FL**: `double` focal length of the pinhole camera.

---

## **Return Value**

- `Eigen::MatrixX2d`: A matrix of size `(N×2)`, where each row is the projected 2D image coordinate `(u, v)` corresponding to an input vertex.

---



## **Example Usage**

### **1. Prepare Input Data**

Define a cuboid using eight corner points in world coordinates. Also specify the camera’s position, look‑at point, and focal length:
```cpp
Eigen::Matrix<double,8,3> testTarget;
testTarget << 748793.66839939367, 2565000.6052587116, 48.909999999999997,
    748791.82177844818, 2565003.9931349289, 48.909999999999997,
    748795.20965466544, 2565005.8397558741, 48.909999999999997,
    748797.05627561093, 2565002.4518796569, 48.909999999999997,
    748793.66839939367, 2565000.6052587116, 50.909999999999997,
    748791.82177844818, 2565003.9931349289, 50.909999999999997,
    748795.20965466544, 2565005.8397558741, 50.909999999999997,
    748797.05627561093, 2565002.4518796569, 50.909999999999997;

Eigen::RowVector3d cameraPosition(748780.10705481586, 2565024.6238759784, 57.51);
Eigen::RowVector3d cameraDirPoint(748794.43902702956, 2565003.2225072929, 49.909999999999997);
double FL = 32.0;
```

<a id="close-lightbox" style="display: none;"></a>
<div class="single-img-container">
    <a href="#lightbox-single-1" class="lightbox-trigger">
        <img src="../fig/worldCoord.png" 
             alt="3D world points"> 
    </a>
</div>

<div id="lightbox-single-1" class="lightbox">
    <a href="#close-lightbox" class="lightbox-close">&times;</a>
    <img src="../fig/worldCoord.png" alt="3D world points (Large)">
</div>

### **2. Perform 3D-to-2D Projection**
```cpp
Eigen::Matrix<double, 8, 2> imagePoints = WorldToCameraImageCoords(testTarget, cameraDirPoint, cameraPosition, FL);
```

### **3. Result**
The projected 2D image coordinates are visualized below:

<div class="single-img-container">
    <a href="#lightbox-single-2" class="lightbox-trigger">
        <img src="../fig/cameralCoord.png" 
             alt="Projected 2D points"> 
    </a>
</div>

<div id="lightbox-single-2" class="lightbox">
    <a href="#close-lightbox" class="lightbox-close">&times;</a>
    <img src="../fig/cameralCoord.png" alt="Projected 2D points (Large)">
</div>

The red rectangle in the image represents the assumed CMOS sensor area of the camera, with a physical size of 6.4 mm × 4.8 mm.

### **Complete Implementation**
```cpp
#include "LiteGeometry.h"

int main() {

    Eigen::Matrix<double,8,3> testTarget;
    testTarget << 748793.66839939367, 2565000.6052587116, 48.909999999999997,
        748791.82177844818, 2565003.9931349289, 48.909999999999997,
        748795.20965466544, 2565005.8397558741, 48.909999999999997,
        748797.05627561093, 2565002.4518796569, 48.909999999999997,
        748793.66839939367, 2565000.6052587116, 50.909999999999997,
        748791.82177844818, 2565003.9931349289, 50.909999999999997,
        748795.20965466544, 2565005.8397558741, 50.909999999999997,
        748797.05627561093, 2565002.4518796569, 50.909999999999997;

    Eigen::RowVector3d cameraPosition(748780.10705481586, 2565024.6238759784, 57.51);
    Eigen::RowVector3d cameraDirPoint(748794.43902702956, 2565003.2225072929, 49.909999999999997);
    double FL = 32.0;
    std::cout << "Camera Position: " << cameraPosition.format(Eigen::FullPrecision) << "\n";
    std::cout << "Camera Look-at Point: " << cameraDirPoint.format(Eigen::FullPrecision) << "\n";
    std::cout << "Focal Length: " << FL << "\n";

    Eigen::Matrix<double, 8, 2> imagePoints = WorldToCameraImageCoords(testTarget, cameraDirPoint, cameraPosition, FL);
    std::cout << "\nProjected Image Coordinates:\n";
    std::cout << imagePoints.format(Eigen::FullPrecision) << "\n";
    
    return 0;
}
```