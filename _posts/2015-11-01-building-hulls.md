---
title: How to get a set of points' convex hull vertices
---

``` python

import numpy as np
from scipy.spatial import ConvexHull
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D

points = np.random.rand(300, 3)
hull = ConvexHull(points)
ax = plt.axes(projection='3d')

for i in xrange(points.shape[0]):
    x, y, z = points[i]
    plt.plot([x], [y], [z], 'o', color='black')

visited = set()

for smplx in hull.simplices:
    for v in smplx:
        x, y, z = points[[v]][0]
        if not (x, y, z) in visited:
            plt.plot([x], [y], [z], 'o', color='green')
            visited.add((x, y, z))
    xs, ys, zs = points[smplx].T
    plt.plot(xs, ys, zs, 'k-', color='green')

plt.show()

```
