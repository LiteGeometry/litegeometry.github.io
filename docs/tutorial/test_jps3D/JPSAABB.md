# **3D JPS Pathfinding (base on `JPSAABBEnv`)**


This document describes how to perform 3D JPS using the `JPSAABBEnv` class.
`JPSAABBEnv` provides high‑precision collision detection through CGAL’s AABB tree.

---

## **Features**

### **`JPSAABBPathFinder` Class**
```cpp
/// JPSAABBPathFinder extends the JPSAABBEnv environment for 3D pathfinding 
class JPSAABBPathFinder : public JPSAABBEnv 
{
public:
    JPSAABBPathFinder(
        const std::unordered_map<size_t, SCData>& SCMap,
        const std::unordered_map<size_t, EFData>& EFMap
    );
    /**
     * Executes JPS graph search between two 3D points
     * @param startPoint  Starting position;
     * @param endPoint    Ending position; 
     * @return A sequence of 3D integer grid coordinates representing the found path
     */
    std::vector<Eigen::RowVector3i> JPSGraphSearch(
        const Eigen::RowVector3d& startPoint, 
        const Eigen::RowVector3d& endPoint 
    );
    }
```

⚠️**Critical Constraint:** 

1. Both `startPoint` and `endPoint` must lie within the navigable space defined by `SCMap`.
2. Neither `startPoint` nor `endPoint` must be located inside any obstacle volume defined by `EFMap`.

---

## **Example Usage**

### **1. Data Preparation**

This example uses [testdata_simple.json](https://github.com/ZhikangLai/lightGeo/testData/testdata_simple.json) from the repository.

Load the **navigable‑corridor volumes** (`SCMap`) and **obstacle volumes** (`EFMap`) from the JSON file.  

```cpp
// Load navigable‑corridor volumes (SCMap) and obstacle volumes (EFMap)
const auto& [SCMap,EFMap] = loadVolumeMaps("./testData/testdata_simple.json");

// Define start/end points within bounds and outside obstacles
Eigen::RowVector3d startPoint(748876.1520, 2564890.4544, 65.5); 
Eigen::RowVector3d endPoint(748678.2699, 2564651.6236, 50.0761);
```
The following map illustrates terrain features, including obstacle zones and the navigable corridor boundaries:
<a id="close-lightbox" style="display: none;"></a>
<div class="single-img-container">
    <a href="#lightbox-single-1" class="lightbox-trigger">
        <img src="../jps/jps_map.png" 
             alt="map"> 
    </a>
</div>

<div id="lightbox-single-1" class="lightbox">
    <a href="#close-lightbox" class="lightbox-close">&times;</a>
    <img src="../jps/jps_map.png" alt="map (Large)">
</div>


### **2. Environment Initialization**
 
Next, initialize the `JPSAABBPathFinder` class with the loaded spatial data.

During construction, the class builds a CGAL‑based `DetectionObjects` structure (AABB tree) and stores it as a private member. This structure is later used to test whether a candidate point lies inside the corridor, on its boundary, or within an obstacle.


```cpp
// Initialize pathfinder with collision detection structures
JPSAABBPathFinder _JPSAABBPathFinder(SCMap, EFMap); // Constructs AABB trees for spatial queries
```

### **3. Path Search Execution**

With the environment initialized, you can now execute JPS between the start and end points:
```cpp
// Perform JPS search
const auto& bestPath = _JPSAABBPathFinder.JPSGraphSearch(startPoint, endPoint);
```

### **4. Results**

The resulting path is a sequence of 3D grid coordinates representing the optimal trajectory.
The figure below visualizes the output:

<div class="single-img-container">
    <a href="#lightbox-single-2" class="lightbox-trigger">
        <img src="../jps/jps1.png" 
             alt="Pathfinding Result"> 
    </a>
</div>

<div id="lightbox-single-2" class="lightbox">
    <a href="#close-lightbox" class="lightbox-close">&times;</a>
    <img src="../jps/jps1.png" alt="Pathfinding Result (Large)">
</div>

- **Obstacles**: Represented in red.
- **Navigable corridor**: Shown in green.
- **JPS path**: Displayed as a blue polyline.


### **Complete Implementation**
```cpp
#include "Prepare.h"
#include "JPSAABBEnv.h"
#include <chrono>  

int main() {
    // Load spatial data
    const auto& [SCMap,EFMap] = loadVolumeMaps("./testData/testdata_simple.json");

    // Define start / end points
    Eigen::RowVector3d startPoint(748876.1520, 2564890.4544, 65.5);
    Eigen::RowVector3d endPoint(748678.2699, 2564651.6236, 50.0761);

    // Initialise path‑finder (constructs AABB trees)
    JPSAABBPathFinder _JPSAABBPathFinder(SCMap, EFMap); 

    // Perform JPS search and measure execution time
    auto t0 = std::chrono::high_resolution_clock::now();
    const auto& bestPaths = _JPSAABBPathFinder.JPSGraphSearch(startPoint, endPoint);
    auto t1 = std::chrono::high_resolution_clock::now();

    auto duration = std::chrono::duration_cast<std::chrono::milliseconds>(t1 - t0);
    std::cout << "JPSAABBPathFinder execution time: " << duration.count() << " ms" << std::endl;

    return 0;
}
```

