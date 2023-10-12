---
layout: post
title: "【Paper阅读】Extracting Triangular 3D Models, Materials, and Lighting From Images Fields for Relighting and View Synthesis"
date: 2022-07-04 14:00:00 +0800
category: 科研
tags: [科研, 深度学习, 逆向渲染, Inverse Rendering]
mathjax: true
description: Unfinshed. This post is about my understanding of nvdiffrec.
---

* content
{:toc}

本篇最大的特点是在渲染这一步基本抛弃了传统NeRF体渲染那一套，而改用传统的渲染管线。但整体思想仍然是可微渲染的思想，然而在训练过程和渲染流程上都有很大改变。

需要注意的是本篇的数据不只需要多视角下的照片，也同样需要环境的mask贴图。



### 拓扑
几何部分主要使用了DMTet进行生成。


### 着色

#### 材质

采用经典PBR（迪士尼提出的版本）。只考虑一个漫反射项以及一个各向同性的GGX镜面项。最大程度减少需要存储的纹理。

#### 纹理

本篇文章采用体坐标进行纹理采样，为了避免这一采样方法带来的大量内存占用，作者选择使用MLP对所有材质纹理进行压缩。作者最终希望训练的一个MLP能够拥有这样一个从时间坐标$x$到材质参数的映射（$k_{d}, k_{orm}, \text{n}$）。采用tiny-cuda-nn框架，以及配套的hash-grid positional encoding（似乎和instant ngp有一些关系）。

在拓扑和纹理都收敛之后，我们将重参数化整个模型，通过MLP重新生成一套纹理映射。