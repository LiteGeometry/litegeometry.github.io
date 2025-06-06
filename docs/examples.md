# Examples and Tutorials

Explore practical implementations of geometric algorithms through detailed, step-by-step examples.

## **Basic Geometry**
Fundamental operations such as constructing closed polygons, computing plane normals, and verifying coplanarity.

- [Generate closed polygons from unordered point sets](tutorial/test_generateClosedPolygon/generateClosedPolygon.md)
  
- [Compute plane normals from 3D coplanar points](tutorial/test_computePlaneNormal/computePlaneNormal.md) 

- [Verify coplanarity of 3D point sets](tutorial/test_isCoplanar/isCoplanar.md) 

## **Point Queries**
Techniques for determining spatial relationships between points and other primitives (lines, polygons, polyhedra).

- [Determine point-line coincidence (2D/3D)](tutorial/test_isPointOnLine/isPointOnLine.md)
  
- [Test point-in-polygon containment (2D/3D)](tutorial/test_isPointInPolygon/isPointInPolygon.md)
  
- [Check point-in-polyhedron containment](tutorial/test_isPointInPolyhedron/isPointInPolyhedron.md)

## **Line & Surface Queries**
Intersection detection and distance computations between lines, polygons, and polyhedra.

- [Compute minimum distance between two 3D line primitives](tutorial/test_isLinesIntersection/computeLinesDistance.md)
  
- [Detect line-line intersections (2D/3D)](tutorial/test_isLinesIntersection/isLinesIntersection.md) 
  
- [Determine line-polygon intersections (2D/3D)](tutorial/test_isLinePolygonIntersection/isLinePolygonIntersection.md)
  
- [Identify line-polyhedron intersections.](tutorial/test_isLinePolyhedronIntersection/isLinePolyhedronIntersection.md)


## **Oriented Bounding Containers**
Compute oriented bounding boxes and rectangles, useful for collision detection and spatial queries.

- [Generate oriented bounding boxes (PCA-aligned and minimum-volume)](tutorial/test_computeBox/computeBox.md)

## **Point Filtering**
Filter points based on containment within geometric boundaries.

- [Filter 2D points by polygon containment](tutorial/test_filterPointsByPolygon/filterPointsByPolygon.md)

- [Filter 3D points by polyhedron containment](tutorial/test_filterPointsByPolyhedron/filterPointsByPolyhedron.md)

## **Pinhole Camera**
Project 3D world coordinates to 2D image planes.

- [Project 3D points to 2D camera coordinates.](tutorial/test_WorldToCameraImageCoords/WorldToCameraImageCoords.md)

## **Rasterisation Toolkit**
Convert continuous geometric shapes into discrete grid representations.

- [Rasterization data structures](tutorial/test_RasterizationTool/RasterizationCustomTypes.md)
  
- [Rasterize lines via Bresenham's algorithm (2D/3D)](tutorial/test_RasterizationTool/rasterizeBresenhamLine.md)
  
- [Rasterize polygon (2D/3D)](tutorial/test_RasterizationTool/rasterizePolygon.md)
  
- [Rasterize polyhedron surfaces](tutorial/test_RasterizationTool/rasterizePolyhedron.md)

## **Path Planning**

Implement and optimize 3D Jump Point Search (JPS) algorithms for large-scale environments.

- [JPS environment models](tutorial/test_jps3D/JPSCustomTypes.md)
  
- [High-precision JPS with CGAL-AABB](tutorial/test_jps3D/JPSAABB.md)
  
- [Voxel-accelerated JPS](tutorial/test_jps3D/JPSVoxel.md)