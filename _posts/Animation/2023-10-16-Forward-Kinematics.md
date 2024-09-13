---
title: Forward Kinematics
tags: [Computer Animation, Kinematics]
style: fill
color: primary
description: A review of cis562's demonstration of forward kinematics.
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

Before we start, about joints.
Data structure of `Joint`

```cpp
struct Joint{
  Joint * parent;
  int length;
  vec3 rotation; // Usually use a euler angle to represent rotation
  vec3 translation;  // 3D translation of one joint
};
```
- Rigid Body
- Degree of freedom

- Left hand axis

![](https://www.oreilly.com/api/v2/epubs/9781788830409/files/assets/a465e4c5-b6ca-4006-a40e-1aa9ad2ebc5d.png)

- Cross product

![](https://images.nagwa.com/figures/explainers/616184792816/2.svg)

### Review
---
**In the notation of this course:**

- Transformation Matrix
    - $F_{j}^{i}$ represents:
        - The transformation matrix of the coordinate of one point $p$ in frame of reference $j$ to the frame of referece $i$.
        - The transformation matrix from frame of reference $i$ to frame of reference $j$.
    - Each column of $F_j^i$ represents the 


![](https://www.researchgate.net/profile/Andrea-Maria-Zanchettin/publication/261021019/figure/fig1/AS:369887865262080@1465199547047/Kinematic-model-of-the-human-arm.png)



### Forward Kinematics
---

- General form of kinematic chain

![Image of leg joints]()

Take this image as an example. Assume that each joints all have 3 degrees of freedom of rotation and no translation.

We can have:

$$
\vec{x} = f(\Theta)
$$

$f$ here represents a function of forward kinematics.

We can further expand the equation that:

$$
\begin{align*}
\textbf{x} &= f(\vec{\Theta}) \\
        &=  F_4^0 \begin{bmatrix} 0 \\ 0 \\  0 \\ 1 \end{bmatrix}\\
        &= F_1^0 F_2^1 F_3^2 F_4^3 \begin{bmatrix} 0 \\ 0 \\  0 \\ 1 \end{bmatrix}
        % &= H_T(\vec{d_{0,1}^0})H_R(\vec{\theta_1})\\ 
        %   &H_T(\vec{d_{1,2}^1})H_R(\vec{\theta_2}) \\
        %   &H_T(\vec{d_{2,3}^2})H_R(\vec{\theta_3}) \\
        %   &H_T(\vec{d_{3,4}^3})H_R(\vec{\theta_4})\begin{bmatrix} 0 \\ 0 \\  0 \\ 1 \end{bmatrix}
\end{align*}
$$

where the transformation matrix $F_j^i$ is

$$
F_j^i = \begin{bmatrix} \textbf{R}_j^i \ \ \textbf{d}_{i, j}^{i} 
  \\ \textbf{0} \ \ \ \ 1
 \end{bmatrix}
$$

Please note here that, $\textbf{x}$ is not a linear combination of $\vec{\Theta}$. We can not write $\textbf{x}$ as $\textbf{x} = \textbf{M}{\vec{\Theta}} $. As $\vec{\Theta}$ here is the variable within each transformation matrix $F$. 

To be more clear about this part:

$$
\begin{align*}
\textbf{x} &= f(\vec{\Theta}) \\
            &= F_1^0(\vec{\Theta}_1) F_2^1(\vec{\Theta}_2) F_3^2(\vec{\Theta}_3) F_4^3(\vec{\Theta}_4) \begin{bmatrix} 0 \\ 0 \\  0 \\ 1 \end{bmatrix}
\end{align*}
$$

(we assume there is no translation within each joint, thus $\textbf{d}$ is fixed)

Above is the basic procedure of forward kinematics. Given the status of each parent joints, we can determine the position of one joint easily through recursively calculating the transformation matrices. 

Forward kinematic is nice! But not enough probably..


### Inverse Kinematics
---

To go through inverse kinematics, we might need to highlight the reason why we would bother developing this method if we have already had one robust method of forward kinematics.

- Scenario 1: Imagine you are a animator, and you want to pose a specific gesture of one character. 
<div align="center">
<img src="https://ssb.wiki.gallery/images/thumb/f/fa/Mii_Swordfighter_SSBU.png/375px-Mii_Swordfighter_SSBU.png">
<figcaption> Mii Swordfighter from Super Smash Bros. Ultimate <figcaption\>
</div>

Unfortunately, this sort of gesture is not that obvious for you to generate using forward kinematics. Take the figure above as an example. When you adjust one joint, all the joints connected as children will also move correspondingly. This brings a lot of trouble to your work.

> Super Smash Bros. Series is one of my favourite video games. Please try. Please...


- Scenario 2: You are developing a game with complicated 3D terrains. Your boss/producer/designer wants you to develop a cool feature that your skeleton can automatically do physical detection when walking inside the scene. With FK, you might not even imagine it possible! Think about all the possible extra job you might need to accomplish within one frame...

<div align="center">
<img src="https://i.ytimg.com/vi/FEMbGLhh_QQ/sddefault.jpg?v=60bbc88d">
<figcaption> Luckily, we can do that with IK now... <figcaption\>
</div>

With IK, this sort of feature can be implemented easily.

Our main goal is to develop a method as **Inverse Kinematics(IK)**. As the name suggests, we will do kinematics inversely. We want to find a function $g$ as an inverse function of $f$ above such that

$$
\vec{\Theta} = g(\textbf{x})
$$


It is not that easy. 

Before we start messing up with math and formula, we better have a rough blueprint about how we are going to solve this problem and why.

The easiest way of finding an inverse function of forward kinematics is to calculate the inverse function explicitly. Bad news is that we probably can't. As stated above, $\textbf{x}$ is a rather complex function of ${\vec{\Theta}}$ 

$$
\begin{align*}
\textbf{x} &= f(\vec{\Theta}) \\
            &= F_1^0(\vec{\Theta}_1) F_2^1(\vec{\Theta}_2) F_3^2(\vec{\Theta}_3) F_4^3(\vec{\Theta}_4) \begin{bmatrix} 0 \\ 0 \\  0 \\ 1 \end{bmatrix}
\end{align*}
$$

With $\vec{\Theta}$ split into pieces and being cosined and sined within each transformation matrix, we cannot find a easy way to inverse this function.

What is the easiest function that can be inversed? Linear functions. According to linear algebra, all linear functions/transformations can be represented as a matrix $A$. To calculate the inverse of this function, we can simply calculate the inverse of this matrix $A^{-1}$ (assuming this matrix is inversible).

Here's the problem, how do we turn this inverse problem into a problem that only contains linear transformation?