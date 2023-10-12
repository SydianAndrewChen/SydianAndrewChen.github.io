---
name: Computer Graphics Course Project
tools: [Mesh Subdivision, Path Tracing]
image: https://www.sketchappsources.com/resources/source-image/movie-badges-jurajjurik.png
description: Projects of my undergraduate computer graphics course. Raytracing, mesh subdivision, and etc.
---


### Mesh Subdivision

#### Edge Flip

An edge flip operation is a geometric transformation
performed on a triangular mesh to improve its quality. It
involves flipping an edge shared by two adjacent triangles,
replacing the old edge with a new one connecting the two
opposite vertices.
To do edge flip, we need to change only the edge-vertex
relationship of related four vertices.

- Screenshots of a mesh before and after a edge flip

![Edge Flip]()

#### Edge Split

Edge split involves creating an entire new vertex and add six
new half-edges, two new edges, and two faces. And we also
need to update all related edges, faces, half-edges and
vertices.
- Screenshots of a mesh before and after a combination of
both edge split and edge flip.

### Loop Subdivision


Loop subdivision is a technique used to improve the quality
of a mesh by smoothing and subdividing its faces in an
iterative manner. To begin, we compute the new positions of
the vertices by averaging their positions with those of their neighboring vertices. Next, we calculate the positions of the new vertices located along the edges of the mesh by averaging the positions of their neighboring vertices and the midpoint of the edge. Finally, we connect the newly created vertices along each edge to form new faces. This process repeats for a predetermined number of iterations or until the desired level of refinement is achieved. Overall, loop subdivision combines smoothing and subdivision operations to enhance the overall quality and smoothness of a mesh.
- Screenshots of teapots after several loop subdivisions



### Pathtracer

