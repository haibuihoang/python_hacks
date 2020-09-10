# Zorder issues when using axes3d

Edit file: anaconda3/lib/python3.8/site-packages/mpl_toolkits/mplot3d/axes3d.py

```
 287         force_zorder=False
 288         if (hasattr(self, 'force_zorder')):
 289            if (self.force_zorder):
 290                force_zorder=True
 291         
 292         # Calculate projection of collections and patches and zorder them.
 293         # Make sure they are drawn above the grids.
 294         zorder_offset = max(axis.get_zorder()
 295                             for axis in self._get_axis_list()) + 1
 296         for i, col in enumerate(
 297                 sorted(self.collections,
 298                        key=lambda col: col.do_3d_projection(renderer),
 299                        reverse=True)):
 300             if (force_zorder):
 301                col.zorder = zorder_offset + col.zorder
 302             else:
 303                col.zorder = zorder_offset + i
 304             #print(col.zorder)
 305         for i, patch in enumerate(
 306                 sorted(self.patches,
 307                        key=lambda patch: patch.do_3d_projection(renderer),
 308                        reverse=True)):
 309             if (force_zorder):
 310                patch.zorder = zorder_offset + patch.zorder
 311             else:
 312                patch.zorder = zorder_offset + i
```

For example (without explicitly zorder, the display goes wrong):
```
import mpl_toolkits.mplot3d as a3
import matplotlib.pylab as plt
import numpy as np    

fig = plt.figure()
ax = a3.Axes3D(fig)
ax.force_zorder=True  #Need this to force the zorder!
# create an orizontal plane
corners = [[0,0,0],[0,5,0],[5,5,0],[5,0,0]]
tri = a3.art3d.Poly3DCollection([corners], alpha=0.9,zorder=-100)
tri.set_color('w')
tri.set_edgecolor('k')
ax.add_collection3d(tri)

# plot a vector
ax.plot([2,2],[2,2],[0,4], c = 'r')

# plot some points
ax.scatter([1,3],[1,3],[1,3], c = 'r')

ax.set_xlim([0, 5.0])
ax.set_ylim([0, 5.0])
ax.set_zlim([0, 2.5]);

plt.show()
```
