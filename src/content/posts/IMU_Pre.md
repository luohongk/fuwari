---
title: 让IMU预积分更加通俗易懂
published: 2026-01-19T12:02:23.000Z
description: IMU预积分详细解读
tags:
  - 技术分享
  - 关于编程
category: 技术分享类
draft: false
---

## 1 致敬

首先致敬优秀的前辈优秀的工作。给后来者铺路搭桥。

[🌐 IMU预积分(四)——VINS-mono中的预积分](https://gutsgwh1997.github.io/2020/05/04/IMU%E9%A2%84%E7%A7%AF%E5%88%86-%E5%9B%9B/)
[🌐 预积分总结与公式推导20180827.pdf](https://github.com/PetWorm/IMU-Preintegration-Propogation-Doc/blob/master/%E9%A2%84%E7%A7%AF%E5%88%86%E6%80%BB%E7%BB%93%E4%B8%8E%E5%85%AC%E5%BC%8F%E6%8E%A8%E5%AF%BC20180827.pdf)
[🌐IMU 预积分 \| Jacob.lsx's Blog](https://sxij.xyz/posts/imu-preintegration/)
[🌐港大郑纯然博士IMU预积分 (解闷版)](https://zhuanlan.zhihu.com/p/1911921324726649326)

> 看了大佬们写的技术分享之后，已经很详细了。于是我想用一种更加通俗易懂的表达方式来推导与描述一下。当前读研生活过半，也经历了一些迷茫。我的科研引路人告诉我，无论怎样，都不要放弃学习与积累。

## 2 为什么要进行IMU预积分？

这个要从IMU这个传感器本身的特点说起，它的特点就是“状态递推”。后一时刻的状态需要用上前一时刻的状态。如果更新了前一时刻的状态之后，后面所有的状态就都要在此计算更新，而这个积分计算比较复杂，因此很麻烦。倒不如积分增量只算一次，每次只根据上一时刻的状态加积分值计算下一时刻的状态。

## 3 基本的测量模型

陀螺仪：
$$\tilde{\boldsymbol{\omega}}_{wb}^b(t) = \boldsymbol{\omega}_{wb}^b(t) + \mathbf{b}_g(t) + \boldsymbol{\eta}_g(t) $$

加速度计：
$$ \tilde{\boldsymbol{f}}^b(t) = \boldsymbol{C}\_b^{w\mathrm{T}} \bigl( \boldsymbol{a}^w + \boldsymbol{g}^w \bigr) + \mathbf{b}\_a(t) + \boldsymbol{\eta}\_a(t)$$
