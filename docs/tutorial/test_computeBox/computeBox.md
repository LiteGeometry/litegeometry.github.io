# **Oriented Bounding Box (OBB) Computation**


This document outlines a collection of functions for computing oriented bounding rectangles and boxes. Two families of algorithms are provided: those that align the bounding shape with the data’s principal components (PCA-aligned) and those that produce the exact minimum-area (2D) or minimum-volume (3D) enclosure.

---

## **Overview**

The API is organised into the following groups:

- **PCA-Aligned Oriented Bounding Boxes/Rectangles (2D & 3D)**: 

    Align the bounding shape with the principal components of the input points. Optionally, the computation can be carried out on the points’ convex hull.

- **Minimum Oriented Bounding Boxes/Rectangles (2D & 3D)**: 

    Compute the rectangle of smallest area (2D) or the box of smallest volume (3D) that encloses all points, using the rotating‑calipers algorithm in 2D and geometric optimisation in 3D.

---

## **2D PCA-Aligned Oriented Bounding Rectangle**

### **Function Signature**
```cpp
// Matrix input
std::pair<Eigen::Matrix<double, 4, 2>, Eigen::RowVector2d> computePCAOBB2D(
    const Eigen::MatrixX2d& points,
    bool useConvexHull = false
);

// Vector input
std::pair<Eigen::Matrix<double, 4, 2>, Eigen::RowVector2d> computePCAOBB2D(
    const std::vector<Eigen::RowVector2d>& points,
    bool useConvexHull = false
);
```

### **Parameters**

- **points**: Matrix of 2D points (`Eigen::MatrixX2d`, one point per row) or a `std::vector<Eigen::RowVector2d>`.

- **useConvexHull** (Default: `false`): If `true`, the convex hull is computed first and PCA is applied to the hull vertices.

### **Return Value**

- A `std::pair` containing:

    **Vertices**: `Eigen::Matrix<double, 4, 2>` — the four rectangle corners, listed counter‑clockwise.

    **Centroid**: `Eigen::RowVector2d` — the rectangle centre.


---

## **3D PCA-Aligned Oriented Bounding Box**

### **Function Signature**
```cpp
// Matrix input
std::pair<Eigen::Matrix<double, 8, 3>, Eigen::RowVector3d> computePCAOBB3D(
    const Eigen::MatrixX3d& points,
    bool useConvexHull = false
);

// Vector input
std::pair<Eigen::Matrix<double, 8, 3>, Eigen::RowVector3d> computePCAOBB3D(
    const std::vector<Eigen::RowVector3d>& points,
    bool useConvexHull = false
);
```

### **Parameters**

- **points**: Matrix of 3D points (`Eigen::MatrixX3d`, one point per row) or a `std::vector<Eigen::RowVector3d>`.

- **useConvexHull** (Default: `false`): If `true`, the convex hull is computed first and PCA is applied to the hull vertices..


### **Return Value**

- A `std::pair` containing:

    **Vertices**: `Eigen::Matrix<double, 8, 3>` — the eight cuboid corners.

    **Centroid**: `Eigen::RowVector3d` — the cuboid centre.

---

## **2D Minimum Oriented Bounding Rectangle**

### **Function Signature**
```cpp
// Matrix input
std::pair<Eigen::Matrix<double, 4, 2>, Eigen::RowVector2d> computeMinOBB2D(
    const Eigen::MatrixX2d& points
);

// Vector input
std::pair<Eigen::Matrix<double, 4, 2>, Eigen::RowVector2d> computeMinOBB2D(
    const std::vector<Eigen::RowVector2d>& points
);
```

### **Parameters**

- **points**: Matrix of 2D points (`Eigen::MatrixX2d`, one point per row) or a `std::vector<Eigen::RowVector2d>`.

### **Return Value**

- A `std::pair` containing:

    **Vertices**: `Eigen::Matrix<double, 4, 2>` — the four rectangle corners, listed counter‑clockwise.

    **Centroid**: `Eigen::RowVector2d` — the rectangle centre.

---


## **3D Minimum Oriented Bounding Box**

### **Function Signature**
```cpp
// Matrix input
std::pair<Eigen::Matrix<double, 8, 3>, Eigen::RowVector3d> computeMinOBB3D(
    const Eigen::MatrixX3d& points
);

// Vector input
std::pair<Eigen::Matrix<double, 8, 3>, Eigen::RowVector3d> computeMinOBB3D(
    const std::vector<Eigen::RowVector3d>& points
);
```
### **Parameters**

- **points**: Matrix of 3D points (`Eigen::MatrixX3d`, one point per row) or a `std::vector<Eigen::RowVector3d>`.

### **Return Value**

- A `std::pair` containing:

    **Vertices**: `Eigen::Matrix<double, 8, 3>` — the eight cuboid corners.

    **Centroid**: `Eigen::RowVector3d` — the cuboid centre.

---

## **Example Usage**
The example below uses [randomPoints.csv](https://github.com/ZhikangLai/LiteGeometry/testData/randomPoints.csv) from the repository.

### **1. Data Preparation**

Load 200 random 3D points from a CSV file into an Eigen matrix:
```cpp
Eigen::Matrix<double, 200, 3> testPointsMat;
std::ifstream file("./testData/randomPoints.csv");
std::string line;
Eigen::Index row = 0;
while (getline(file, line) && row < 200) {
    std::istringstream lineStream(line);
    std::string c1, c2, c3;
    if (std::getline(lineStream, c1, ',') &&
        std::getline(lineStream, c2, ',') &&
        std::getline(lineStream, c3)) {

        testPointsMat.row(row) << stod(c1), stod(c2), stod(c3);
        row++;
    }
}
```

<a id="close-lightbox" style="display: none;"></a>

<div class="img-grid">
    <a href="#lightbox-1" class="lightbox-trigger">
        <img src="../fig/randomPoint2d.png" alt="2D随机点分布" loading="lazy">
    </a>
    <a href="#lightbox-2" class="lightbox-trigger">
        <img src="../fig/randomPoint3d.png" alt="3D随机点分布" loading="lazy">
    </a>
</div>

<div id="lightbox-1" class="lightbox">
    <a href="#close-lightbox" class="lightbox-close">&times;</a> 
    <img src="../fig/randomPoint2d.png" alt="2D 大图">
</div>
<div id="lightbox-2" class="lightbox">
    <a href="#close-lightbox" class="lightbox-close">&times;</a> 
    <img src="../fig/randomPoint3d.png" alt="3D 大图">
</div>




### **2. Running the Bounding-Box Algorithms**
With the data ready, compute both PCA-aligned and minimum-area/volume bounding shapes:

```cpp
{
    // 2D PCA Rectangle vs Minimum Rectangle
    Eigen::Matrix<double, 4, 2> pcaRect = computePCAOBB2D(testPointsMat.leftCols<2>(), true).first;
    Eigen::Matrix<double, 4, 2> mbRect = computeMinOBB2D(testPointsMat.leftCols<2>()).first;

    std::cout << "\n--- PCA 2D Bounding Rectangle (XY) ---\n";
    std::cout << pcaRect.format(Eigen::FullPrecision) << "\n";

    std::cout << "\n--- Minimum 2D Bounding Rectangle (XY) ---\n";
    std::cout << mbRect.format(Eigen::FullPrecision) << "\n";
}

{ 
    // 3D PCA Box vs Minimum Box
    Eigen::Matrix<double, 8, 3> pcaBox = computePCAOBB3D(testPointsMat, true).first;
    Eigen::Matrix<double, 8, 3> mbBox = computeMinOBB3D(testPointsMat).first;

    std::cout << "\n--- PCA 3D Bounding Box ---\n";
    std::cout << pcaBox.format(Eigen::FullPrecision) << "\n";

    std::cout << "\n--- Minimum 3D Bounding Box ---\n";
    std::cout << mbBox.format(Eigen::FullPrecision) << "\n";

} 
```

### **3. Results**
The figure below compares the two approaches:
<div class="single-img-container">
    <a href="#lightbox-single" class="lightbox-trigger">
        <img src="../fig/computeBox.png" 
             alt="Bounding Box Comparison"> 
    </a>
</div>

<div id="lightbox-single" class="lightbox">
    <a href="#close-lightbox" class="lightbox-close">&times;</a>
    <img src="../fig/computeBox.png" alt="Bounding Box Comparison (Large)">
</div>


- PCA‑aligned shapes typically enclose slightly more area or volume than the true minimum because they are restricted to the principal axes.

> 💡**Tip:** Use the PCA‑based OBB for its speed and suitability for large point clouds. Select the minimum‑area/volume OBB only when the exact smallest enclosure is required.

### **Complete Implementation**

```cpp
#include "LiteGeometry.h"

int main() {
    
    Eigen::Matrix<double, 200, 3> testPointsMat;
    std::ifstream file("./testData/randomPoints.csv");
    std::string line;
    Eigen::Index row = 0;
    while (getline(file, line) && row < 200) {
        std::istringstream lineStream(line);
        std::string c1, c2, c3;
        if (std::getline(lineStream, c1, ',') &&
            std::getline(lineStream, c2, ',') &&
            std::getline(lineStream, c3)) {

            testPointsMat.row(row) << stod(c1), stod(c2), stod(c3);
            row++;
        }
    }

    {
        // 2D PCA Rectangle vs Minimum Rectangle
        Eigen::Matrix<double, 4, 2> pcaRect = computePCAOBB2D(testPointsMat.leftCols<2>(), true).first;
        Eigen::Matrix<double, 4, 2> mbRect = computeMinOBB2D(testPointsMat.leftCols<2>()).first;

        std::cout << "\n--- PCA 2D Bounding Rectangle (XY) ---\n";
        std::cout << pcaRect.format(Eigen::FullPrecision) << "\n";

        std::cout << "\n--- Minimum 2D Bounding Rectangle (XY) ---\n";
        std::cout << mbRect.format(Eigen::FullPrecision) << "\n";
    }

    { 
        // 3D PCA Box vs Minimum Box
        Eigen::Matrix<double, 8, 3> pcaBox = computePCAOBB3D(testPointsMat, true).first;
        Eigen::Matrix<double, 8, 3> mbBox = computeMinOBB3D(testPointsMat).first;

        std::cout << "\n--- PCA 3D Bounding Box ---\n";
        std::cout << pcaBox.format(Eigen::FullPrecision) << "\n";

        std::cout << "\n--- Minimum 3D Bounding Box ---\n";
        std::cout << mbBox.format(Eigen::FullPrecision) << "\n";

    }   

    return 0;
}
```
