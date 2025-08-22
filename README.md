# Box3-Octree

## Description
A lightweight and simple Octree for three.js that uses Box3 instead of Meshes.
By using the uber-efficient THREE.Box3 as an alternative to THREE.Mesh for space partitioning, you get rid of a lot of unused memory.
On the other hand, official Octree shiped with three.js uses geometry for intersection test against Sphere / Capsule. As a result, this is not applicable because there is no geometry in Box3.
This library solves that issue, by using a globalRegion in the broad-phase, and .containsPoint() function in the narrow-phase. 

## Features
- The tree is built recursively by subdividing the global bounding volume.
- Each node stores only indices of boxes that intersect that region.
- Sphere-based spatial queries (e.g. “find boxes near a point”).
- Efficient filtering using Box3.containsPoint.
- This implementation assumes static boxes; position updates requires re‑insert.
- ES6 module, ready for use with import maps or modern build tools.

## Install
```js
import * as THREE from 'three';
import { Box3Octree } from './Box3Octree.js';
```

## Usage
```js
// clone all Box3's into a global bounding volume
const boxes = /* your array of Box3s */;
const globalRegion = boxes.reduce((acc, b) => acc.union(b), boxes[0].clone());

// builds tree and insert Boxes
const tree = new Box3Octree(globalRegion);
boxes.forEach((_, idx) => tree.insert(boxes, idx));

// set sphere search size and center
const point = new THREE.Vector3(0, 0, 0);
const sphere = new THREE.Sphere(point, 2);

// query tree & filter by containsPoint
const candidates = tree.querySphere(boxes, sphere);
const results = [...candidates]
  .map(i => boxes[i])
  .filter(b => b.containsPoint(point));
```

## API
#### `new Box3Octree(globalRegion)`
Builds a tree instance

#### `tree.insert(boxes, idx)`
Inserts a THREE.Box3 array + each index

#### `tree.querySphere(boxes, sphere)`
Searches for boxes using a THREE.Sphere

## To Do
- [x] Basic API
- [x] "Search by sphere" method implemented
- [ ] "Search by cuboid" method implemented
- [ ] Pass `global bounding volume` generation to private function
- [ ] Pass `containsPoint(point)` to private function

