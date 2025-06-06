# **Rasterization Custom Types**

This document explains the predefined custom types used in the `RasterizationTool`.
The `RowVector2iSet` and `RowVector3iSet` types are designed to efficiently manage sets of 2D and 3D integer points, respectively. Both utilize `absl::flat_hash_set` as their underlying container, with custom hash and equality functors to ensure efficient storage and retrieval of Eigen vector types.


## **Point-Set Type Aliases**
### `RowVector2iSet`

```cpp
using RowVector2iSet = absl::flat_hash_set<Eigen::RowVector2i, EigenRowVectoriHash, EigenRowVectoriEqual>;
```


This alias represents a set of unique 2D integer points, with each point stored as an `Eigen::RowVector2i`.

It provides fast lookup and insertion using `absl::flat_hash_set`, and is well-suited for 2D grid-based rasterization and planar geometry processing tasks.

### `RowVector3iSet`

```cpp
using RowVector3iSet = absl::flat_hash_set<Eigen::RowVector3i, EigenRowVectoriHash, EigenRowVectoriEqual>;
```

This alias defines a set of unique 3D integer points stored as `Eigen::RowVector3i`.

It offers the same performance benefits as the 2D variant, and is particularly suitable for large-scale point cloud processing and 3D spatial indexing, due to the combination of `absl::flat_hash_set` and customized hashing and equality comparisons.


## **Hash and Equality Functors**

### `EigenRowVectoriHash`

The `EigenRowVectoriHash` struct provides the hash functor required by `absl::flat_hash_set`. 

It overloads `operator()` to support both 2D and 3D vectors, and uses the `xxhash` algorithm for high-performance hashing.


```cpp
static constexpr uint64_t kSeed = 0x9E3779B97F4A7C15ULL;
struct EigenRowVectoriHash {
    size_t operator()(const Eigen::RowVector2i& v) const noexcept {
        return static_cast<size_t>(xxh::xxhash<64>(v.data(), sizeof(int) * 2, kSeed));
    }

    size_t operator()(const Eigen::RowVector3i& v) const {
        return static_cast<size_t>(xxh::xxhash<64>(v.data(), sizeof(int) * 3, kSeed));
    }
};
```

### `EigenRowVectoriEqual`


The `EigenRowVectoriEqual` struct defines the equality functor used for comparing `Eigen::RowVector2i` and `Eigen::RowVector3i` instances.

It enables proper duplicate detection in the hash set by comparing the vectors element-wise.

```cpp
struct EigenRowVectoriEqual {
    bool operator()(const Eigen::RowVector2i& v1, const Eigen::RowVector2i& v2) const noexcept {
        return v1 == v2;
    }

    bool operator()(const Eigen::RowVector3i& v1, const Eigen::RowVector3i& v2) const noexcept {
        return v1 == v2;
    }
};
```


