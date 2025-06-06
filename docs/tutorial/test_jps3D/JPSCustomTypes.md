# **3D Jump Point Search Environment Models**


In large-scale 3D maps for UAV obstacle avoidance, we have developed an efficient, reliable path-planning framework based on the 3D Jump Point Search (JPS) algorithm. This system employs advanced environment modeling and spatial decomposition techniques to enhance both preprocessing and query performance.


This document introduces two preprocessing methods designed for JPS-based 3D path-finding in expansive environments. Each method employs a distinct spatial representation that balances collision-checking accuracy and runtime speed.

---

**Reference for 3D JPS:**  
Liu, S., Watterson, M., Mohta, K., Sun, K., Bhattacharya, S., Taylor, C. J., & Kumar, V. (2017). Planning dynamically feasible trajectories for quadrotors using safe flight corridors in 3D complex environments. *IEEE Robotics and Automation Letters*, 2(3), 1688-1695.  
DOI: [10.1109/LRA.2017.2663526](https://ieeexplore.ieee.org/abstract/document/7839930) 

---

## **Core Components**

### **`GridNode` Structure**
Represents a node in the 3D search grid:
```cpp
struct GridNode {
    Eigen::RowVector3i position;          // Discrete 3D coordinates
    std::vector<Eigen::RowVector3i> dirs; // Valid movement directions
    double gCost = INF;                   // Cost from start to current node
    double fCost = INF;                   // Estimated total cost (g + heuristic)
    GridNode* parent = nullptr;           // For path back-tracking
    priorityQueue::handle_type heapkey;   // Handle for open-list heap
};
```

---

### **Diagonal Direction Decomposition Mapping (`DirDecsMap`)**

#### **Definition**
Maps a primary diagonal 3D direction vector to its possible decomposition directions.

```cpp
using DirDecsMap = std::unordered_map<
    Eigen::RowVector3i, 
    std::vector<Eigen::RowVector3i>, 
    EigenRowVectoriHash
>;
```

#### **Key Points**
1. **Diagonal Decomposition** 
    For any diagonal direction `(dx, dy, dz)` where at least two components are non-zero:
    - Axis-aligned components: `(dx, 0, 0)`, `(0, dy, 0)`, `(0, 0, dz)`
    - Plane-diagonal components: `(dx, dy, 0)`, `(dx, 0, dz)`, `(0, dy, dz)`

2. **Zero Vector Special Case**
   When querying `(0,0,0)`, returns all 26 possible 3D directions:
   ```
   // Zero-vector returns all 26 possible directions
   Eigen::RowVector3i(0, 0, 0) → [
       (±1, 0, 0), (0, ±1, 0), (0, 0, ±1),
       (±1, ±1, 0), (±1, 0, ±1), (0, ±1, ±1),
       (±1, ±1, ±1)
   ]
   ```

#### **Example**
```cpp
// Get precomputed directional decomposition mappings
DirDecsMap dirDecs = getDirDecsMap();

// Get decomposition for diagonal (1,-1,1)
auto& components = dirDecs.at({1, -1, 1});
/* Returns:
{ {1,0,0}, {0,-1,0}, {0,0,1},  // Axis-aligned
{1,-1,0}, {1,0,1}, {0,-1,1} }  // Plane-diagonal */

```

> Use `DirDecsMap getDirDecsMap();` to generate the complete mapping.

---


### **Orthogonal Directions Mapping (`OrtDirsMap`)**

#### **Definition**
Maps principal axis direction to its orthogonal direction set:

```cpp
using OrtDirsMap = std::unordered_map<
    Eigen::RowVector3i, 
    std::vector<Eigen::RowVector3i>, 
    EigenRowVectoriHash
>;
```

#### **Key Points**
1. **Principal Axes Coverage**  
   Contains mappings for six principal directions:
   ```text
   (±1, 0, 0)  // X-axis
   (0, ±1, 0)  // Y-axis
   (0, 0, ±1)  // Z-axis
   ```

2. **Orthogonal Vector Composition**  
   For each principal direction `(dx, dy, dz)`:
   ```text
   Orthogonal vectors = [
       (0, ±1, 0),   // Y-axis
       (0, 0, ±1),   // Z-axis
       (0, ±1, ±1)   // YZ-plane combinations
   ]
   ```

#### **Example**
```cpp
// Get precomputed orthogonal direction mappings
OrtDirsMap ortDirMap = getOrtDirsMap();

// Retrieve orthogonal directions for X-axis
auto& x_ortho = ortDirMap.at({1, 0, 0});
/* Returns:
{ {0,1,0}, {0,-1,0},      // Pure Y-axis
  {0,0,1}, {0,0,-1},      // Pure Z-axis
  {0,1,1}, {0,1,-1},      // YZ-plane positive
  {0,-1,1}, {0,-1,-1} }   // YZ-plane negative */
```

> Use `OrtDirsMap getOrtDirsMap();` to generate complete orthogonal mappings.
> 
---



## **Collision Detection Models**

### **Model 1: CGAL-AABB Spatial Query (`JPSAABBEnv`)**

Defines `DetectionObjects` using CGAL's AABB Tree for precise geometric queries.

```cpp
typedef CGAL::Exact_predicates_inexact_constructions_kernel K;
typedef K::Point_3 Point_3;
typedef CGAL::Surface_mesh<Point_3> Mesh;
typedef CGAL::AABB_face_graph_triangle_primitive<Mesh> Primitive;
typedef CGAL::AABB_traits<K, Primitive> AABB_Traits;
typedef CGAL::AABB_tree<AABB_Traits> Tree;
typedef CGAL::Side_of_triangle_mesh<Mesh, K> Point_inside;
struct DetectionObjects {
    Mesh scUnion;                                    // Unified navigable mesh
    std::unique_ptr<Tree> scTree;                    // CGAL AABB spatial index
    std::unique_ptr<Point_inside> insideSCDetecter;  // Bounded side checker

    DetectionObjects(Mesh&& sc) : scUnion(std::move(sc)) {
        if (!scUnion.is_empty()) {
            scTree = std::make_unique<Tree>(faces(scUnion).first, faces(scUnion).second, scUnion);
            insideSCDetecter = std::make_unique<Point_inside>(*scTree);
        }
    }
};
```

#### **`JPSAABBEnv` Class**
A high-precision, boolean-based environment model:
```cpp
class JPSAABBEnv {
public:
    JPSAABBEnv(const std::unordered_map<size_t, SCData>& SCMap,
            const std::unordered_map<size_t, EFData>& EFMap) {
        initGridMap(SCMap, EFMap); // Users must implement initGridMap according to their operational environment
    }

protected:
    /**​
    * Checks collision status for a 3D grid position 
    * @param point Discrete grid coordinates to validate 
    * @return true if position is obstacle-free and not in boundary zones,
    *          false otherwise 
    */
    inline bool isMovable(const Eigen::RowVector3i& point) const { 
        return (*detector->insideSCDetecter)(Point_3(point.x(), point.y(), point.z())) 
               == CGAL::ON_BOUNDED_SIDE;
    }

private:
    std::unique_ptr<DetectionObjects> detector;
    void initGridMap(const std::unordered_map<size_t, SCData>& SCMap,
                     const std::unordered_map<size_t, EFData>& EFMap);
};
```

---

### **Model 2: Voxelized Surface Mapping (`JPSVoxelEnv`)**

Uses a `RowVector4iSet` (a customized `absl::flat_hash_set` with `xxHash`) to store rasterized voxels that enclose the surfaces of both navigable corridors and obstacles. Each voxel (`Eigen::RowVector4i`) contains:

- **Grid Coordinates**: First three values `(x, y, z)` as integer grid indices.
- **Type Identifier**: The fourth dimension distinguishes:
    - **Boundary voxels of the navigable corridor** (labeled 0): 
        - Rasterized from the outer faces of the navigable polyhedron.
        - Define the exact limits that the drone is not allowed to pass.
    - **Obstacle Surface Voxels** (labeled 1): 
        - Rasterized from the faces of the obstacle polyhedra.
        - Represents collision surfaces for real‑time avoidance.  

By storing both boundary and obstacle surface voxels in a single `RowVector4iSet`, the drone can perform real-time collision checks efficiently as it moves in integer-coordinate steps within the unified grid.


```cpp
static constexpr uint64_t kSeed = 0x9E3779B97F4A7C15ULL;
struct EigenRowVector4iHash {
    size_t operator()(const Eigen::RowVector4i& v) const {
        return static_cast<size_t>(xxh::xxhash<64>(v.data(), sizeof(int)*3, kSeed));
    }
};

struct EigenRowVector4iEqual {
    bool operator()(const Eigen::RowVector4i& v1, const Eigen::RowVector4i& v2) const {
        return v1.head<3>() == v2.head<3>();
    }
};

using RowVector4iSet = absl::flat_hash_set<
    Eigen::RowVector4i,
    EigenRowVector4iHash,
    EigenRowVector4iEqual
>;
```

#### **`JPSVoxelEnv` Class**
A voxel-based discrete environment model:

```cpp
class JPSVoxelEnv {
public:
    JPSVoxelEnv(const std::unordered_map<size_t, SCData>& SCMap,
            const std::unordered_map<size_t, EFData>& EFMap) : GridPointsSet() {
        initGridMap(SCMap, EFMap);  // Users must implement initGridMap according to their operational environment
    }

protected:
    /**​
    * Checks collision status for a 3D grid position 
    * @param point Discrete grid coordinates to validate 
    * @return true if position is obstacle-free and not in boundary zones,
    *          false otherwise 
    */
    inline bool isMovable(const Eigen::RowVector3i& point) const { 
        return GridPointsSet.find(
            Eigen::RowVector4i(point.x(), point.y(), point.z(), 0) // 4th element not used in comparison
        ) == GridPointsSet.end();
    }

private:
    RowVector4iSet GridPointsSet;
    void initGridMap(const std::unordered_map<size_t, SCData>& SCSet,
                    const std::unordered_map<size_t, EFData>& EFSet);
};
```

---

## **Performance Comparison**

| Metric                | `JPSAABBEnv` (CGAL-AABB)       | `JPSVoxelEnv` (Voxel Hashing)         |
|-----------------------|------------------------------|------------------------------------|
| **Query Time Complexity**        | O(log N)                     | O(1)                               |
| **Best Suited For**   | High-precision collision checks  | Fast, large-scale real-time queries |

