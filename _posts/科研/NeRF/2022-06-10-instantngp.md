---
layout: post
title: "【Paper阅读】Instant Neural Graphics Primitives with a Multiresolution Hash Encoding"
date: 2022-06-10 21:05:00 +0800
category: 科研
tags: [科研, 深度学习, NeRF]
description: Unfinshed. This post is about my understanding of instantngp.
---

[主页](https://nvlabs.github.io/instant-ngp/)

本篇文章更多的是提供一种快速做Encoding的方式，将原来的Positional Encoding替换成Hash Encoding，同时在Hash上对数据结构进行了设计，又使用了一个MLP来进行Hash上的优化（避免碰撞），因此本篇不仅仅是局限于Nerf，也可以用于其他相关的Task。