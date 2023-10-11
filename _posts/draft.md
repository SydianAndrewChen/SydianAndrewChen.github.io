为什么不能直接靠MLP猜uv呢？

首先对于每个物体，选择一个被遮挡最少的view，选择这个view下的mask，创建一个naive的uv mapping，用这个uv mapping在材质库里面做knn缩小范围

输入一个语义、rgb值、世界坐标，输出一个uv值之后采样BRDF（问题在于材质的计算需要知道光源信息，也就是说事先是需要知道光源信息的，是不是可以利用nerf去生成一个环境贴图呢？）

f(semantics, x, d) = u 如何解决degenerate？肯定会有几个点映射到同一个uv上去，考虑借鉴neutex里面，建立一个inverse mapping来监督
sample(u, mat) = a, r, n
shade(x, a, r, n) = c

L(I , I_) 



3D场景上的点到2D纹理的映射是否可以通过MLP建立？