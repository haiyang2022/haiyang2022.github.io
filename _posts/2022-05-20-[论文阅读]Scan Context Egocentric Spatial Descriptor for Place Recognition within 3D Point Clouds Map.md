Paper reading 01

*[1] G. Kim and A. Kim, “Scan Context: Egocentric Spatial Descriptor for Place Recognition Within 3D Point Cloud Map,” in IEEE International Conference on Intelligent Robots and Systems, Dec. 2018, pp. 4802–4809. doi: 10.1109/IROS.2018.8593953.*

## Ⅰ 摘要

相较于用于视觉场景的各类检测子与描述子，使用结构化信息描述一个位置的研究相对较少。近来SLAM的进展提供了稠密的3D环境地图并且可由多种传感器进行定位。针对基于结构化信息的全局定位，我们提出了Scan Context，这是一种从3D激光帧中得到的不基于直方图的全局描述子。与已有的方法不同，本文提出的方法直接从传感器中记录可见空间内的3D结构，并且不依赖于直方图或预训练。此外，该方法提出使用相似度得分去计算两个上下文帧之间的距离，同时使用一种两段式的搜索算法去有效的检测回环。上下文扫描以及它的搜索算法使得回环检测对LiDAR视角具有不变性，故在如反向重访和转角等地点可以检测到回环。Scan context的性能已经通过多种3D激光帧基准数据集进行了评估，本文提出的方法性能有充分的提升。

## Ⅱ Scan context概述

Scan context 是一种区别于传统的基于直方图或基于特征点的3D激光雷达描述子，主要用途是做激光重定位和回环检测，它会对一帧点云以激光为中心进行点云分块，首先在半径方向将点云分为若干个等距的圆环（黄色部分），再在水平方向将点云分成等圆心角的扇形（青色部分）（原始论文中分了20个圆环和60个扇形），则每一个圆环与扇形的重叠部分（黑色部分）就是一个分好的区域（bin），计算每一个区域内Z值的最大值，该值即为该区域Scan context值（区域为空赋0值），将所有区域的Scan context值记录为一个矩阵，这个矩阵的行数等于扇形区域的数量，列数等于圆环的数量，将这个矩阵（单通道），用颜色表（jet colormap）表示出来，即可得到此帧点云的Scan context值。

之后作者定义了一个公式来计算两帧点云Scan context描述子的位置相似度（实质是计算两帧点云所有列向量夹角余弦值的和），该值越小相似度越高。在计算位置相似度时计算量太大，通过将圆环编码为旋转不变的一维描述子ring key（一维数组，数组中每个元素为第i个圆环ring的编码值，ri排布距离由近到远），对数据进行压缩与降维，此后两个Scan context匹配时只对比ring key的相似度，不再计算余弦距离。同时，为ring key构建kd-tree加速查找候选相似帧。（在开源程序中使用了类似的sector key来对数据做降维和加速搜索）

[assets/img/paper01.png](https://github.com/haiyang2022/haiyang2022.github.io/blob/main/assets/img/paper01.png)

## Ⅲ 总结

Scan context has three components: 

(1) the representation that preserves absolute location information of a point cloud in each bin;

​	The reason for using the maximum height of points in each bin is to efficiently summarize the vertical shape of surrounding structures without requiring heavy computations to analyze the characteristics of the point cloud. In addition, the maximum height says which part of the surrounding structures is visible from the sensor.

(2) efficient bin encoding function;

(3) two-step search algorithm;

Note: Scan context is  designed for **outdoor** place recognition.

在本文中，我们提出了一种空间描述子，Scan Context，它将一个地点概括为一个精确描述在以自我为中心的环境中的2.5D结构化信息的矩阵。相较于已有的使用点云的全局描述子，在多种数据集中Scan Context显示出了更高的回环检测性能。在未来的工作中，我们计划通过引入额外的图层拓展Scan Context。即使用其他的区块（bin）编码结构（例如区块的语义信息）来在例如复杂城市LiDAR数据集这类高度重复化结构的数据集中提高性能。
