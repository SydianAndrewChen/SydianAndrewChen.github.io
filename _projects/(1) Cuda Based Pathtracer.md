---
name: Cuda Based Pathtracer
tools: [Cuda, Raytracing]
image: https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/robots.png?raw=true
description: Pathtracer based on pbrt, and implemented with cuda.
---
CUDA Path Tracer
================
[pbrt]: https://pbrt.org/

* Tested on: Windows 10, AMD Ryzen 5800 HS with Radeon Graphics CPU @ 3.20GHz 16GB, NVIDIA GeForce RTX3060 Laptop 8GB

### Implemeted Feature 

- [x] Core
    - [x] Stream Compaction
    - [x] Diffuse & Specular
    - [x] Jittering (Antialiasing)
    - [x] First Bounce Cache
    - [x] Sort by material
- [x] Load gltf
- [x] BVH && SAH
- [x] Texture mapping & bump mapping
- [x] Environment Mapping
- [x] Microfacet BSDF
- [x] Emissive BSDF (with Emissive Texture)
- [x] Direct Lighting
- [x] Multiple Importance Sampling
- [x] Depth of Field
- [x] Tone mapping && Gamma Correction


### Demos

- Emissive Robot Car

<img src="https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/emissive_robot_car_2.png?raw=true"/>

- Metal Bunny

    <img src="https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/result_bunny.png?raw=true"/>

- Texture Mapping & Bump Mapping

<img src="https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/bump_mapping_after.png?raw=true"/>

- Multiple Robots (Depth of Field)

<img src="https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/robots.png?raw=true" />

- Indoor Scene

<img src="https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/indoor.png?raw=true" />

- Video Demo

<video controls>
  <source src="https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/realtime.mp4?raw=true" type="video/mp4">
</video>


### `gltf` Load

In this pathtracer, supported scene format is `gltf` for its high expressive capability of 3D scenes. Please view [this page](https://registry.khronos.org/glTF/specs/2.0/glTF-2.0.html) for more details about gltf. 

Eventually, during development, most scenes used for testing is directly exported from Blender. This enables a much higher flexibility for testing. 

- `scenes/pathtracer_robots_demo.glb` [Link](https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/tree/main/scenes/pathtracer_robots_demo.glb)

![Alt text](https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/blender_ref.png?raw=true)
### BVH
On host, we can construct and traverse BVH recursively. While in this project, our code run on GPU. Though recent cuda update allows recursive function execution on device, we cannot take that risk as raytracer is very performance-oriented. Recursive execution will slow down the kernel function, as it may bring dynamic stack size. 

Thanks to [this paper](https://arxiv.org/pdf/1505.06022.pdf), a novel BVH constructing and traversing algorithm called MTBVH is adopted in this pathtracer. This method is **stack-free**.

This pathtracer only implements a simple version of MTBVH. Instead of constructing 6 BVHs and traversing one of them at runtime, only 1 BVH is constructed. *It implies that this pathtracer still has the potential of speeding up*.

- With BVH & Without BVH:
<table>
    <tr>
        <th>With BVH</th>
        <th>Without BVH</th>
    </tr>
    <tr>
        <th><img src="https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/with_bvh_bunny.png?raw=true" /></th>
        <th><img src="https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/without_bvh_bunny.png?raw=true" /></th>
    </tr>
</table>

As expected, speedup is huge up to 40 times. With a more complex scene, BVH should give a higher speedup.

### Texture Mapping & Bump Mapping

To enhance the details of mesh surfaces and gemometries, texture mapping is a must. *Here we have not implemented mipmap on GPU, though it should not be that difficult to do so*.

- `scenes/pathtracer_test_texture.glb` [Link](https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/tree/main/scenes/pathtracer_test_texture.glb)

<table>
    <tr>
        <th>Before bump mapping</th>
        <th>After bump mapping</th>
    </tr>
    <tr>
        <th><img src="https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/bump_mapping_before.png?raw=true"/></th>
        <th><img src="https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/bump_mapping_after.png?raw=true"/></th>
    </tr>
</table>

### Microfact BSDF

To use various material, bsdfs that are more complicated than diffuse/specular are required. Here, we will first implement the classic microfacet BSDF to extend the capability of material in this pathtracer.

This pathtracer uses the Microfacet implementation basd on [pbrt].

Metallness = 1. Roughness 0 to 1 from left to right.

> Please note that the sphere used here is not an actual sphere but an icosphere. 

- `scenes/pathtracer_test_microfacet.glb` [Link](https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/tree/main/scenes/pathtracer_test_microfacet.glb)

![Microfacet Demo](https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/microfacet.png?raw=true)

With texture mapping implemented, we can use `metallicRoughness` texture now. Luckily, `gltf` has a good support over metallic workflow.

- `scenes/pathtracer_robot.glb` [Link](https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/tree/main/scenes/pathtracer_robot.glb)

![Metallic Workflow Demo](https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/metallic_workflow.png?raw=true)

### Direct Lighting & MIS

> To stress the speed up of convergence in MIS, Russian-Roulette is disabled in this part's rendering.

> The tiny dark stripe is visible in some rendering result. This is because by default we do not allow double-sided lighting in this pathtracer.

> By default, number of light sample is set to 3.

When sampling for the direction of next bounce, we have adopted importance sampling for bsdf for most of the time. It enhances the convergence speed for specular materials, as the sampling strategies greatly aligned with the expected radiance distribution on hemisphere. However, for diffuse/matte surfaces, this sampling strategies can be optimized, as the most affecting factors for the radiance distribution of these sort of materials is light instead of outgoing rays. Thus, sampling from light is also a valuable strategy to speedup convergence speed of raytracing rough surfaces.

In this demo scene, 3 metal plane are allocated with 4 cube lights. When we only sample bsdf, we can see that the expected radiance on the surface of metal plane converges. When we only sample light, we can see how the rougher part of the scene, the back white wall, has better converging speed. Hence, we are looking forward to a sampling strategy that combines the advantages of these two, which is multiple importance sampling.

- `scenes/pathtracer_mis_demo.glb` [Link](https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/tree/main/scenes/pathtracer_mis_demo.glb)

<table>
    <tr>
        <th>Only sample bsdf 500spp</th>
        <th>Only sample light 500spp</th>
        <th>MIS 500spp</th>
    </tr>
    <tr>
        <th><img src="https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/mis_bsdf_500spp.png?raw=true"/></th>
        <th><img src="https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/mis_light_500spp.png?raw=true"/></th>
        <th><img src="https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/mis_mis_500spp.png?raw=true"/></th>
    </tr>
</table>


To see more details about this part, see [this part of pbrt](https://www.pbr-book.org/3ed-2018/Monte_Carlo_Integration/Importance_Sampling) or [this post](https://sydianandrewchen.github.io/blog/PBRT-Direct-Lighting) of mine.


Test on bunny scene. Faster convergence speed can be observed.

- `scenes/pathtracer_bunny_mis.glb` [Link](https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/tree/main/scenes/pathtracer_bunny_mis.glb)

<table>
    <tr>
        <th>Without MIS 256spp</th>
        <th>With MIS 256spp</th>
    </tr>
    <tr>
        <th><img src="https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/mis_without_mis_bunny_256spp.png?raw=true"/></th>
        <th><img src="https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/mis_with_mis_bunny_256spp.png?raw=true"/></th>
    </tr>
    <tr>
        <th>Without MIS 5k spp</th>
        <th>With MIS 5k spp</th>
    </tr>
    <tr>
        <th><img src="https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/mis_without_mis_bunny_5000spp.png?raw=true"/></th>
        <th><img src="https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/mis_with_mis_bunny_5000spp.png?raw=true"/></th>
    </tr>
</table>




### Depth of Field

In depth of field, we define two variables. `focal_length` & `aperture`. 

More details can be viewed in [this post](https://pathtracing.home.blog/depth-of-field/).

![](https://pathtracinghome.files.wordpress.com/2018/12/dof1.png?w=1089)

<table>
    <tr>
        <th>Depth of Field (Aperture=0.3)</th>
    </tr>
    <tr>
        <th><img src="https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/depth_of_field.png?raw=true" class="responsive"/></th>
    </tr>
</table>

<video controls>
  <source src="https://github.com/SydianAndrewChen/Project3-CUDA-Path-Tracer/blob/main/img/realtime_depth_of_field.mp4?raw=true" type="video/mp4">
</video>

### Future (If possible)

#### Cuda Side
- [ ] More cuda optimization
    - [ ] Bank conflict
    - [ ] Loop unroll
        - [ ] Light sample loop (if multiple light rays) 
    - [ ] Higher parallelism (Use streams?)
- [ ] Tile-based raytracing
    - [ ] Potentially, it should increase the rendering speed, as it will maximize locallity within one pixel/tile. No more realtime camera movement though.

#### Render Side
- [ ] Adaptive Sampling
- [ ] Mipmap
- [ ] ReSTIR
- [ ] Refractive
- [ ] True B**S**DF (Add some subsurface scattering if possible?)
- [ ] Volume Rendering ~~(Ready for NeRF)~~