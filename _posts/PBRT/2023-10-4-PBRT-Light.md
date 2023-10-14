---
title: PBRT Light
tags: [pbrt, light]
style: fill
color: primary
description: A review of pbrt's explanation of light from a physical perspective.
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

### Light Source

> All objects with temperature above absolute zero have moving atoms. In turn, as described by Maxwell’s equations, the motion of atomic particles that hold electrical charges causes objects to emit electromagnetic radiation over a range of wavelengths. As we’ll see shortly, most of the emission is at infrared frequencies for objects at room temperature; objects need to be much warmer to emit meaningful amounts of electromagnetic radiation at visible frequencies.

- Intuition: Light is associated with temperature and wavelength.
    - Light emit different amount of energy over different wavelength.
    - With energy absorbed from non-absolute-zero temperature, light can emit different amount of energy.
    - Light source convert energy into emittted electromagnetic radiation. 


- Luminous efficacy
    - Measures how effectively a light source converts power to visible illumination.
        
    $$
        \frac{\int \Phi_e(\lambda)V(\lambda) d\lambda}{\int\Phi_i(\lambda)d\lambda}
    $$

    - where $V(\lambda)$ denotes the [spectural response curve](https://pbr-book.org/3ed-2018/Color_and_Radiometry/Radiometry#sec:photometry)
    - Luminous efficacy has units of **lumens per watt**.
    - A typical value of luminous efficacy for an incandescent tungsten lightbulb is around 15 lm/W.


### Blackbody Emitters
- All objects can absorb, reflect, and emit electromagnetic wave.
    - For incoming radiance, object either absorb or reflect.
    - Object either emit or reflect to generate outgoing radiance.
    - For most of the object, the electromagnetic wave emitted from them is not visible.
- A blackbody is a perfect emitter with no reflection:
> Blackbodies are so-named because they absorb absolutely all incident power, reflecting none of it.

- What about the emitted radiance?
    - *They do emit radiance. However, in most of the case, light emitted from blackbody is invisible for human. If the temperature is high enough(around 4000K for example), blackbody is not black anymore.*

### Planck's Law

- Planck's Law gives the radiance emitted by a blackbody as a function of wavelength $\lambda$ and temperature $T$ measrured in Kelvins.

$$
L_e(\lambda, T) = \frac{2hc^2}{\lambda^{5}(\text{e}^{\frac{hc}{\lambda k_{b} T}}-1)}
$$

- $h, c, k_b$ are [constants](https://pbr-book.org/3ed-2018/Light_Sources/Light_Emission#eq:plancks-law).

### Stefan-Boltzman Law
- Gives the radiant exitance at a point p for a blackbody emitter

$$
M(p) = \sigma T^4
$$

- $\sigma$ is a [constant](https://pbr-book.org/3ed-2018/Light_Sources/Light_Emission#eq:stefan-boltzmann)。

### Wien's Displacement Law
> Because the power emitted by a blackbody grows so quickly with temperature, it can also be useful to compute the normalized SPD for a blackbody where the maximum value of the SPD at any wavelength is 1. This is easily done with Wien’s displacement law, which gives the wavelength where emission of a blackbody is maximum given its temperature:

- Gives the wavelength that blackbody emits the maximum radiance at a given temperature.

$$
\lambda_{max} = \frac{b}{T}
$$

- $b$ is a [constant](https://pbr-book.org/3ed-2018/Light_Sources/Light_Emission#eq:wien-displacement).


### Kirchoff's Law
- The emitted radiance distribution at any frequency is equal to the emission of a perfect blackbody at that frequency times the fraction of incident radiance a tthe frequenc that is absorbed by the object. (This relationship follows from the object being assumed to be in the thermal equiibrium.) **The fraction of radiance absorbed is equal to 1 minus the amount reflected, and so the emitted radiance is**

$$
L^{'}_{e}(T, \omega, \lambda) = L_{e}(T, \lambda)(1-\rho_{hd}(\omega))
$$

- where $L_e{T, \lambda}$ is the emitted radiance given by [Planck's Law](https://pbr-book.org/3ed-2018/Light_Sources/Light_Emission#eq:plancks-law), and $\rho_{hd}(\omega)$ is the [hemispherical-directional reflectance](https://pbr-book.org/3ed-2018/Reflection_Models/Basic_Interface.html#eq:rho-hd).

### Color Temperature

- Represents the shift in the spectrum.
    - High color temperature: lower wavelength, cool color
    - Low color temperature: higher wavelength, warm color
    - Please note that color temperature is not object temperature.
        - *For blackbody, color temperature should be equal to object temperature?*
        