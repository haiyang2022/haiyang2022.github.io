---
layout: post
title: '[论文阅读]LeGO-LOAM Lightweight and Ground-Optimized Lidar Odometry and Mapping on Variable Terrain'
subtitle: 'LeGO-LOAM'
date: 2022-05-21
categories: paper
tags: paper LOAM

---

Paper reading 02

[1] *T. Shan and B. Englot, “LeGO-LOAM: Lightweight and Ground-Optimized Lidar Odometry and Mapping on Variable Terrain,” in IEEE International Conference on Intelligent Robots and Systems, Dec. 2018, pp. 4758–4765. doi: 10.1109/IROS.2018.8594299.*

## 摘要

我们提出了一种轻量且对地面优化的激光里程计和建图方法，LeGO-LOAM，用于对地面车辆进行实时的六自由度位姿估计。LeGO-LOAM是轻量的，它能够在低功耗的嵌入式系统中达到实时位姿估计。它也是地面优化的，它在分割和优化步骤中充分利用了地面的存在。我们首先应用点云分割来滤除噪声，并进行特征提取来得到不同的平面和边缘特征。之后使用平面与边缘特征进行一个两步式的LM优化方法去解出连续扫描帧中的六自由度变换分量。基于利用地面车辆从可变平面环境收集的数据集，我们对比了LeGO-LOAM与其他的SOTA方法（LOAM），LeGO-LOAM在减少计算消耗的同时达到了相似或更好的精度。我们也将LeGO-LOAM整合进一个SLAM框架去消除位姿估计误差引起的漂移，这部分使用KITTI数据集进行了测试。

## 方法

LeGO-LOAM在地面优化部分提出一种两步式的前端匹配，首先从分割出的地面点云中提取平面特征，并通过平面特征的匹配获取六自由度姿态中的$ [t_z,\theta_{roll},\theta_{pitch}] $,第二步通过匹配角点特征来获取剩余三自由度的位姿$[t_x,t_y,\theta_{yaw}]$.LeGO-LOAM中同样引入回环检测来校正运动估计的偏差。

## 流程

整个系统可以分为以下五个模块：

1. segmentation：获取单帧点云并将它投影为深度图像进行分割，被分割的点云传入feature extraction模块，在将点云投影为深度图后，对深度图分区，区块内点数小于30则去除（为了去除树叶、草等不能保证前后两帧观测相同的物体）；
2. feature extraction：特征提取；
3. lidar odometry：激光里程计使用从上一模块中提取出的特征来寻找连续帧之间的相对变换（进行点到线和点到面的扫描匹配）；
4. lidar mapping：将特征匹配至全局点云地图；
5. transform integration：融合lidar odometry和lidar mapping的位姿估计结果并输出最终的位姿估计。

## 总结与讨论

我们提出了LeGO-LOAM,一种轻量并进行地面优化的激光里程计和建图方法，用于在复杂环境中对UGV进行实时的位姿估计。LeGO-LOAM是轻量的，它能被用在嵌入式系统中，并且达到实时性能。LeGO-LOAM同时也进行地面优化，充分利用了地面分离、点云分割和改进的LM算法。在这种处理中，一些代表非可信特征的无价值点被滤除。一种两步式的LM优化计算出位姿变换的不同部分。本文提出的方法用一系列UGV室外环境数据集进行了测试。测试结果显示LeGO-LOAM与SOTA方法LOAM相比，能达到相似或更佳的精度。LeGO-LOAM的计算时间也大幅减少。未来的工作包括探索该方法在其他类型车辆上的应用。

虽然LeGO-LOAM针对地面车辆进行了特别的位姿优化，它的潜在应用可以被扩展到其他设备（只需做一点修改），例如无人机（UAV）。当将LeGO-LOAM应用到UAV时，我们不能假设一帧扫描中存在地面。一帧点云可以被分割而不进行地面提取。特征提取过程与获取$ F_e ,f_e,f_p $相同。$F_p$中的特征将被从所有分割点中选取，而不是从被标记为地面点的点云中提取。接下来原始的LM算法将被用于获取两帧之间的位姿变换，而不是使用一种两步式的优化方法。虽然在做了这些改变之后计算时间会增加，LeGO-LOAM依然是高效的，因为大量的点在户外噪声环境中已经通过分割去除。得益于分割，估计的特征对应点对精度可能会提高。此外，LeGO-LOAM的在线进行回环检测的能力是它完成长时间导航任务的有效工具。

![paper02.png](https://github.com/haiyang2022/haiyang2022.github.io/raw/main/_posts/paper02.png)