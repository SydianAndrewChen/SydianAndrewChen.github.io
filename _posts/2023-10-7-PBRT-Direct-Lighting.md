---
title: PBRT Direct Lighting
tags: [pbrt, direct lighting]
style: fill
color: primary
description: A review of pbrt's explanation of light from a physical perspective.
mathjax: true
---

<script>
    MathJax = {
        tex: {
        inlineMath: [['$', '$'], ['\\(', '\\)']]
        }
    };
    </script>
<script type="text/javascript" id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>    

### Why this part?
> You could skip this part if you are not interested.

- Before entering the main part, I should state why I need to summarize this part of pbrt. I am trying to implement a cuda pathtracer. Now I am stuck at the part of direct light sampling. I thought I have figured out how to do this part. However, after debugging for two days, I found out that my result of directly lighting seems to have rather little contribution of the final result. And I could not figure out why directly adding a part of direct lighting will not damage the original convergence of old path tracing algorithm. Intuitively, I felt that there should be a weight to average between the radiance of direct lighting and indirect lighting(multi-bounces). But I could not know how I should calculate this weight from other posts online. Eventually, I decided to read this part of pbrt thoroughly to find out why my result is so weird.



### Importance Sampling
- Why do we need the direct lighting?
    - According to pbrt, mostly because we want to do multi-importance sampling(MIS)
- Importance sampling basically refers to a kind of sample strategy.
    - When using Monte-Carlo techniques, we should make effort to increase the likelihood between $p(x)$ and $f(x)$.
    - This will decrease the variance between different estimators.
    - For one sort of distribution, getting the strategy of importance sampling is not that hard.
        - For BSDF alone, we have spent a lot of time implementing its `sample_f` method to sample a `wi` with the highest probability.
        - For light alone, we shall use **direct lighting**.


### Direct Lighting

To sample light, there are two phases.
- First, we should decide the strategies to pick up lights
    - Pick one randomly from all lights
    
    or
    - Pick all lights and add them together.

    To understand the two strategies, first recall the rendering equation at a surface:

    $$
    L_o(p, \omega_o) = \int_{S^2}{f(p, \omega_o, \omega_i) \ L_d(p, \omega_i) \ |\cos(\theta_{i})| \ \text{d}\omega_i}
    $$

    This can be broken into a sum over the n lights in the scene.

    $$
    \sum_{j=1}^{n} \int_{\text{S}^2}f(p, \omega_o, \omega_i) \ L_{d(j)}(p, \omega_i) \ | \cos{\theta} | \ \text{d}\omega_i
    $$
    
    where $L_{d(j)}$ denotes the incident radiance from the $j\text{th}$ light and 
    
    $$
    L_d(p, \omega_i) = \sum_j{L_{d(j)}(p, \omega_i)}
    $$

    - One valid approach is to estimate each term of the sum individually, adding the results together, i.e., to [sample all lights](https://pbr-book.org/3ed-2018/Light_Transport_I_Surface_Reflection/Direct_Lighting#UniformSampleAllLights). 

    



- Then, for the light sampled, we should subsequently sample a direction of the `Li`. Here we will just discuss on the area light.
    - Sample a point on the shape of the area light uniformly.
        - To guarantee the uniform sample within the shape, we will develop differet sample strategies for different shapes.
        - As we sample uniformly, the pdf of sample shape would be $\frac{1}{A}$.
    - Remember, we are integrating over $\omega$. While we are sampling $A$. We need to consider how to convert the pdf. See [this part](https://pbr-book.org/3ed-2018/Color_and_Radiometry/Working_with_Radiometric_Integrals#IntegralsoverArea).
    ![](https://pbr-book.org/3ed-2018/Color_and_Radiometry/Differential%20solid%20angle%20of%20dA.svg)




---

- The problem is: **How will deal with the combination of multiple distribution?**
### Multiple Importance Sampling
> In many cases, the integrand is the product of more than one function. It can be difficult to construct a PDF that is similar to the complete product, but finding one that is similar to one of the multiplicands is still helpful.

- For example, consider the distribution of bsdf and light together:

$$
L_o(p, \omega_o) = \int_{S^2}{f(p, \omega_o, \omega_i) \ L_d(p, \omega_i) \ |\cos(\theta_{i})| \ \text{d}\omega_i}
$$

- If we were to performa importance sampling to estimate this integral according to distributions based on either $L_d$ or $f$, one of these two will often perform poorly.

> Consider a near-mirror BRDF illuminated by an area light where $L_d$’s distribution is used to draw samples. Because the BRDF is almost a mirror, the value of the integrand will be close to 0 at all $\omega_i$  directions except those around the perfect specular reflection direction. This means that almost all of the directions sampled by $L_d$ will have 0 contribution, and variance will be quite high. Even worse, as the light source grows large and a larger set of directions is potentially sampled, the value of the PDF decreases, so for the rare directions where the BRDF is non-0 for the sampled direction we will have a large integrand value being divided by a small PDF value. While sampling from the BRDF’s distribution would be a much better approach to this particular case, for diffuse or glossy BRDFs and small light sources, sampling from the BRDF’s distribution can similarly lead to much higher variance than sampling from the light’s distribution.

- Summary: For different materials, the distribution of brdfs and the distribution of potential incoming light rays can be significantly different. In practice: 
    - For specular materials, sampling from brdf is better.
    - For diffuse materials, sampling from light is better.

- We cannot add the estimator together, though this solution seems the most obvious. Because variance is additive, adding two estimator may result in a higher variance.


