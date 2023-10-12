---
layout: post
title: "【Paper阅读】PhotoScene: Photorealistic Material and Lighting Transfer for Indoor Scenes
"
date: 2022-07-05 14:00:00 +0800
category: 科研
tags: [科研, 深度学习, 逆向渲染, Inverse Rendering]
mathjax: true
description: Unfinshed. This post is about my understanding of PhotoScene.
---

本篇文章为Adobe出品。我发现Adobe和NV这种公司出文章就特别喜欢搞自己的小体系，基本自己只用自己的东西，也是挺有意思。
和nvdiffrec给我的感觉类似，前置工作比较多，而且工程化比较强，很多工作真的是拿来就用。
目前可以看到的比较重要的前置工作：
- [InvRenderNet](https://cseweb.ucsd.edu/~viscomp/projects/CVPR20InverseIndoor/)
    - 3.1中用来生成逐像素光照以及法线，多余出来的材质参数会用在之后结果的比较中
- [MaskFormer](https://openreview.net/pdf?id=0lz69oI5iZP)
    - 3.1中用来生成逐像素材质的mask，对材质生成语义mask
- [MATch](http://match.csail.mit.edu/)
    - MATch基本算是Adobe自己提供的一个过程生成的、图形化的、参数化的材质库。因此能够表示的材质也受这几条限制。然而这种表示能力在室内场景里似乎也比较合适。




