# **computeLinesDistance**
Computes the closest points and shortest distance between two 3D line primitives (segments, rays, or infinite lines).


## **Overview**

Supports `Segment3D`, `Ray3D`, and `Line3D`. Each type must expose `P1` and `P2` members of type `Eigen::RowVector3d`, representing its endpoints.

---

## **Function Signatures**
```cpp
template<typename LineT>
std::tuple<Eigen::RowVector3d, Eigen::RowVector3d, double> computeLinesDistance(
    const LineT& geoAB, const LineT& geoCD)；
```


## **Parameters**
- **geoAB** (`LineT`): The first 3D line primitive (e.g., `Segment3D`, `Ray3D`, `Line3D`). Must provide members `P1` and `P2` of type `Eigen::RowVector3d`.

- **geoCD** (`LineT`): The second 3D line primitive, with the same requirements as `geoAB`.



## **Return Value**
A `std::tuple` containing:

  1. Closest point on geoAB (`Eigen::RowVector3d`).
  2. Closest point on geoCD (`Eigen::RowVector3d`).
  3. Shortest distance between the two line primitives (`double`).


---

## **Example Usage**

```cpp
#include "lightGeo.h"

int main() {
    {
        Eigen::RowVector3d A(1, 1, 0);
        Eigen::RowVector3d B(0, -1, 0.4);
        Eigen::RowVector3d C(1, -1, 0);
        Eigen::RowVector3d D(0, 0, 0.5);
        Segment3D AB{ A ,B };
        Segment3D CD{ C ,D };
        const auto& [closestP1, closestP2, dist] = computeLinesDistance(AB, CD);
        std::cout << "Closest Point on Segment AB: " << closestP1.format(Eigen::FullPrecision) << "\n";
        std::cout << "Closest Point on Segment CD: " << closestP2.format(Eigen::FullPrecision) << "\n";
        std::cout << "Distance: " << dist << "\n";
    }

    {
        Eigen::RowVector3d A(1, 1, 0);
        Eigen::RowVector3d B(0, 0.5, 0.55);
        Eigen::RowVector3d C(1, -1, 0);
        Eigen::RowVector3d D(0, 0, 0.45);
        Ray3D AB{ A ,B };
        Ray3D CD{ C ,D };
        std::tuple<Eigen::RowVector3d, Eigen::RowVector3d, double> Intersection;
        const auto& [closestP1, closestP2, dist] = computeLinesDistance(AB, CD);
        std::cout << "Closest Point on Ray AB: " << closestP1.format(Eigen::FullPrecision) << "\n";
        std::cout << "Closest Point on Ray CD: " << closestP2.format(Eigen::FullPrecision) << "\n";
        std::cout << "Distance: " << dist << "\n";
    }
    
    return 0;
}
```


<a id="close-lightbox" style="display: none;"></a>
<div class="single-img-container">
    <a href="#lightbox-single" class="lightbox-trigger">
        <img src="../fig/computeLinesDistance.png" 
             alt="Intersection of line primitives with a polyhedron"> 
    </a>
</div>

<div id="lightbox-single" class="lightbox">
    <a href="#close-lightbox" class="lightbox-close">&times;</a>
    <img src="../fig/computeLinesDistance.png" alt="Intersection of line primitives with a polyhedron (Large)">
</div>