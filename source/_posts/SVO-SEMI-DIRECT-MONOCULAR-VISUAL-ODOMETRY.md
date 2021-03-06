---
title: SVO ( SEMI-DIRECT MONOCULAR VISUAL ODOMETRY)
date: 2017-09-15 23:36:21
tags:
---

SVO 简介
---
**SVO 全称 Semi-direct monocular Visual Odometry（半直接视觉里程计），是苏黎世大学机器人感知组的克里斯蒂安.弗斯特（[Christian Forster][Chris]，主页：Christian Forster）等人，于2014年ICRA会议上发表的工作，随后在github开源：[uzh-rpg/rpg_svo][github]。2016年扩展了多相机和IMU之后，写成期刊论文，称为SVO 2.0，预定将在IEEE Trans. on Robotics上发表（视频见[2]）。SVO 2.0目前未开源（个人认为以后也不会开)**<!--more-->

SVO主要工作由弗斯特完成，此外他也在乔治亚理工的gtsam组呆过一段时间，参与了gtsam中IMU部分，文章发表在RSS 2015上，亦是VIO当中的著名工作[3]。此文章后续亦有期刊版本，预计也在TRO上发表。不过会议论文中公式推导有误，而且弗斯特本人似乎只参与了实现部分，没怎么管公式推导……当然这些都是八卦，不谈了。与SVO相关工作是同一组的REMODE[4]，实现了在SVO定位基础上的单目稠密建图（需要GPU），由SVO的二作马蒂亚（Matia Pizzoli）完成。

SVO，虽然按照作者的理解，称为“半直接法”，然而按照我个人的理解，称为“稀疏直接法”可能更好一些。众所周知，VO主要分为特征点法和直接法。而SVO的实现中，混合使用了特征点与直接法：它跟踪了一些关键点（角点，没有描述子，由FAST实现），然后像直接法那样，根据这些关键点周围的信息，估计相机运动以及它们的位置。这与特征点法（以ORB-SLAM为代表）那样，需要对每张图像提取特征点和描述子的实现，是有明显不同的。所以，作者称之为“半直接法”。

不过，由于SVO跟踪的“关键点”，亦可以理解成“梯度明显的像素”。从这个角度来看，它和LSD-SLAM[5]更加相近。只是LSD-SLAM跟踪了所有梯度明显的像素，形成半稠密地图；而SVO只跟踪稀疏的关键点，所以不妨称之为“稀疏直接法”。这一点，和今年慕尼黑理工丹尼尔.克莱默（Daniel Cremers）组的雅各布.恩格尔（Jakob Engel）提出的DSO[6]是很相似的。（PS：ORB,LSD,SVO几个作者似乎都去了同一个公司啊……那我还搞啥……）

在视觉里程计中，直接法最突出的优点是非常快（ORB作者劳尔曾认为特征点比较快，我觉得是不对的）。而在直接法中（包括稀疏的，半稠密的以及稠密的），使用稀疏的直接法，既不必费力去计算描述子，也不必处理像稠密和半稠密那么多的信息，能够达到极快的速度。因此，SVO即使在低端计算平台上也能达到实时性，而在PC平台上则可以达到100多帧每秒的速度。在作者后续工作SVO 2.0中，速度更达到了惊人的400帧每秒。这使得SVO非常适用于计算平台受限的场合，例如无人机、手持AR/VR设备的定位。无人机也是弗斯特等人最初开发SVO的目标应用平台。

SVO另一特点是实现了一种特有的深度滤波器（Depth Filter）。这里一种基于均匀——高斯混合分布的深度滤波器，由弗吉亚兹于2011年提取并推导[7]。由于原理较为复杂，之后再详细解释。SVO将这种滤波器用于关键点的深度估计，并使用了逆深度作为参数化形式，使之能够更好地计算特征点位置。这里SVO在建图线程中的主要任务。

开源版的SVO代码清晰易读，十分适合读者作为第一个SLAM实例进行分析。初学者可以从SVO或ORB开始读起，弗斯特写代码一直比较清楚。

**整个过程分为两个大模块：追踪与建图（与PTAM类似）。**

- 上半部分为追踪部分。主要任务是估计当前帧的位姿。又分为两步：

    + 先把当前帧和上一个追踪的帧进行比对，获取粗略的位姿。
    + 然后根据粗略的位姿，将它与地图进行比对，得到精确的位姿并优化见到的地图点。随后判断此帧是否为关键帧。如果为关键帧就提取新的特征点，把这些点作为地图的种子点，放入优化线程。否则，不为关键帧的时候，就用此帧的信息更新地图中种子点的深度估计值。

- 下半部分为建图部分。主要任务是估计特征点的深度。因为单目SLAM中，刚提的特征点是没有深度的，所以必须用新来的帧的信息，去更新这些特征点的深度分布，也就是所谓的“深度滤波器”。当某个点的深度收敛时，用它生成新的地图点，放进地图中，再被追踪部分使用。

整个SVO架构要比ORB简单一些（ORB有三个线程，且要处理关键帧的共视关系和回环检测），所以效率也要高一些。下面详细谈这两个模块的做法。



追踪（Tracking）部分
-------------------
追踪部分理解难点主要有两个：

- 如何计算帧与帧之间位姿变换？
- 如何计算帧与地图之间的位姿变换？

下面分别来说这两点。

1. Frame-to-Frame的位姿变换

    追踪的第一步是将当前帧与上一个追踪成功的帧进行对比，粗略估计当前帧的位姿。该问题的基本形式为：已知上一帧对地图点的观测（包括2D投影位置和深度），以及当前帧的图像，如何计算当前帧的位姿？用数学语言说，已经k-1帧的位姿$T_{k-1}$，并且知道它的观测量$u_i$, i=1, \ldots, N时，求解$T_{k-1,k}$。

在SVO里，该问题被称为 Model-based Image Alignment （带有相机模型的图像配准），实际上就是我们平时说的稀疏直接法。直接法的原理在我的博客中给出过比较细的推导：[直接法 – 半闲居士 – 博客园][blog] ，此外我也讲过一次讲座：[直接法的原理与实现_高翔_bilibili_演讲•公开课_科技_bilibili_哔哩哔哩弹幕视频网][bilibili] 。本质上，它通过最小化前一帧和当前帧之间的光度误差来求得当前帧的（粗略）位姿：

要理解它，你需要非线性优化的基本知识。同时，为了求目标函数相对于位姿的导数，你需要学习一些李代数的知识。这在我的博客和讲座中均有比较详细的探讨：

+ [视觉slam第4章_演讲•公开课_科技_bilibili_哔哩哔哩弹幕视频网][bilibili2]
+ [非线性优化与g2o录像：http://pan.baidu.com/s/1c2qPdle][baidupan]

由于不想在知乎打公式，所以请读者去看视频和讲座以了解其中原理（因为都已经讲过一遍了）。实现当中，SVO自己实现了高斯——牛顿法的迭代下降，并且比较取巧地使用了一种反向的求导方式：即雅可比在k-1帧图像上进行估计，而不在k帧上估计。这样做法的好处是在迭代过程中只需计算一次雅可比，其余部分只需更新残差即可（即G-N等式右侧的$-J^{T}e$）。这能够节省一定程度的计算量。另一个好处是，我们能够保证k-1帧的像素具有梯度，但没法保证在k帧上的投影也具有梯度，所以这样做至少能保证像素点的梯度是明显的。

实现当中另一个需要注意的地方是金字塔的处理。这一步估计是从金字塔的顶层开始，把上一层的结果作为下一层估计的初始值，最后迭代到底层的。顶层的分辨率最小，所以这是一个由粗到精的过程（Coarse-to-Fine），使得在运动较大时也能有较好的结果。

值得一提的是，完全可以使用优化库，例如g2o或ceres来实现所有的步骤。在优化库中，可以选用更多的优化方式，而且L-M或Dogleg的结果也会比G-N更有保证。我自己就用两个库各实现过一遍。

2. Frame-to-Map

    在1求解之后，我们得到了当前帧位姿的粗略估计。因为它是基于上一帧的结果来计算的，所以如果把它当作真实位姿估计的话，将有较大的累积误差。因此，需要进一步和地图之间进行特征点比对，来对当前帧位姿进一步优化。主要步骤如下：

    + 遍历地图中的所有点，计算在当前帧的投影位置。由于当前帧有粗略的位姿估计，这个投影位置应该与真实位置有少量误差（2~3个像素）。
    + 对每个成功投影的地图点，比较这些点的初始观测图像与当前帧的图像。通过计算光度的误差，求取更精准的投影位置。这步类似于光流，在SVO中称为Refinement。
    + 根据更精确的投影位置，进行位姿与地图点的优化。这一步类似于Bundle Adjustment，但SVO实现中，是把Pose和Point两个问题拆开优化的，速度更快。
    + 判断是否生成关键帧，处理关键帧的生成。

这里理解的难点是，地图点初次被观测到的图像与当前帧的图像进行比对时，不能直接对两个图像块求差，而需要计算一个仿射变换（Affine Warp）。这是因为初次观测和当前帧的位移较远，并且可能存在旋转，所以不能单纯地假设图像块不变。仿射变换的计算方式在PTAM论文的5.3节有介绍，似乎是一种比较标准的处理方式。（其实SVO的追踪部分和PTAM整个儿都挺像。）

实现当中可能还需要注意一些细节。例如有些地方使用了网格，以保证观测点的均匀分布。还有Affine Warp当中需要注意特征点所在的金字塔层数，等等。

此后的Bundle Adjustment部分和传统的区别不大，除了把pose和point分开计算之外。关键帧判断方面，SVO是比较薄弱的（考虑的东西太少），和ORB相比差了不少。



Mapping部分
------
Mapping部分主要是计算特征点的深度。如前所言，单目VO中，刚刚从图像中提取的热乎的关键点是没有深度的，需要等相机位移之后再通过三角化，再估计这些点的深度。这些尚未具备有效深度信息的点，不妨称之为种子点（或候选点）。然而，三角化的成功与否（以及精度），取决于相机之间的平移量和视线的夹角，所以我们通常要维护种子点的深度分布，而不是单纯的一个深度值

牵涉到概率分布的，往往都是理论一大堆屁话，实际可以操作的只有高斯分布一种——高斯只要在计算机里存均值和协方差即可。在逆深度[8]流行起来之后，用逆深度的高斯分布成了SLAM中的标配。然而SVO却使用了一种高斯——均匀混合分布的逆深度（由四个参数描述），推导并实现了它的更新方式，称为Depth Filter。它的工作逻辑大概是这样的：

+ 如果进来一个关键帧，就提取关键帧上的新特征点，作为种子点放进一个种子队列中。
+ 如果进来一个普通帧，就用普通帧的信息，更新所有种子点的概率分布。如果某个种子点的深度分布已经收敛，就把它放到地图中，供追踪线程使用。

当然实现当中还有一些细节，比如删掉时间久远的种子点、删掉很少被看到的种子点等等。

要理解Depth Filter，请搞清楚这两件事：

1. 基于高斯——均匀的滤波器，在理论上的推导是怎么样的？
2. Depth Filter又是如何利用普通帧的信息去更新种子点的？

第1个问题，请参照论文[4],[7],以及[7]的补充材料，以及补充材料的补充材料。相信研究SVO的人应该都推导过，并不很难，静下心来推一遍即可，我当时也就一块小白板就推完了。在SLAM群的群文件里有一个depth filter.pdf，也给出了推导过程：</br>
或者请看[SVO原理解析 – 路游侠 – 博客园][SVO] 。我觉得应该用不着把公式在知乎上再敲一遍……

第2个问题，你需要搞明白极线搜索这件事。由于种子点的深度不确定，它在别的帧里看起来就在一条直线（极线）上：</br>
于是你从这条极线的一个端点走到另一个端点，把每处的图像块都和参考的去比较，就可以（可能）找到正确的匹配了。哦别忘了要Affine Warp一下……找到之后，让depth filter更新其深度分布即可。当然如果位移太小或视线平行性太高，让深度变得更加不确定也是有可能的。在理想情况下，你可以期待一个地图点经过不断观测之后收敛的过程。

评述
---------------
以上就是SVO的基本工作原理了。那么，这样一套系统实际工作起来效果如何呢？相比于其他几个开源方案有何优劣呢？

首先要澄清一点的是：开源版本的SVO，是一个比较挫的版本。相比于LSD或ORB，我还很少看到有人能一次性把SVO跑通的。但是从论文上看，开源版本并不能代表SVO的真实水平。所以应该是心机弗斯特开源了一个只有部分代码的，不怎么好用的版本，仅供学习研究使用。相比之下，DSO，LSD，ORB至少能够在自己数据集上顺利运行，而ORB、LSD还能在大部分自定义的相机上运行，这点开源版本的SVO是做不到的。

那么，抛开开源实现，从理论和框架上来说，SVO有何优劣呢？

优点：

+ 着实非常快，不愧为稀疏直接法；
+ 关键点分布比较均匀；

缺点：（不是我嫌弃它，确实有一堆可以吐槽的地方）

1. 首先这货是VO，不是SLAM，没有闭环。这意味着丢失后没法重定位——丢了基本就挂了。
2. 追踪部分：SVO首先将当前帧与上一个追踪的帧比较，以求得粗略的位姿估计。这里存在一个问题：这必须要求上一个帧是足够准确的！那么问题就来了：怎么知道上一个帧是准的呢？开源SVO里甚少考虑出错的情况。如果上一个帧由于遮挡、模糊等原因丢失，那么当前帧也就会得到一个错误的结果，导致和地图比对不上。又由于这货是没法重定位的，所以就。。。挂了呗。。。
3. 还是追踪部分。既然是直接法，SVO就有直接法的所有缺点。后面那位同学来背一遍直接法缺点？

    + 怕模糊（需要全局曝光相机）
    + 怕大运动（图像非凸性）
    + 怕光照变化（灰度不变假设）

4. 地图部分：

    + Depth Filter收敛较慢，结果比较严重地依赖于准确的位姿估计。如果统计收敛的种子点的比例，会发现并不高，很多计算浪费在不收敛的点上。
    + 相比于纯高斯的逆深度，SVO的Depth Filter主要特点是能够通过Beta分布中的两个参数a,b来判断一个种子点是否为outlier。然而在特征点法中我们也能够通过描述来判断outlier，所以并不具有明显优势。


小结
---------------
1. SVO是基于稀疏直接法的视觉里程计，速度非常快。
2. 代码清晰易读，研究SVO会有不少启发。
3. 但是开源实现存在诸多缺点，不实用。论文中效果应该不是这个开源代码能够实现的。


参考文献</br>
[1] Foster et al., SVO: Fast semi-direct monocular visual odometry, ICRA 2014.</br>
[2] SVO 2.0  </br>
[3] Foster et al., IMU preintegration on manifold for efficient visual-inertial maximum-a-posteriori estimation, RSS 2015.</br>
[4] Pizzoli, Matia and Forster, Christian and Scaramuzza, Davide, REMODE: Probabilistic, monocular dense reconstruction in real time, ICRA 2014.</br>
[5] Engel, Jakob and Schops, Thomas and Cremers, Daniel, LSD-SLAM: Large-scale direct monocular SLAM, ECCV, 2014.</br>
[6] Engel, Jakob and Koltun, Vladlen and Cremers, Daniel, Direct sparse odometry, 2016.</br>
[7] George Vogiatzis and Carlos Hernández, Video-based, real-time multi-view stereo, Image and Vision Computing, 2011.</br>
[8] Civera, Javier and Davison, Andrew J and Montiel, JM Martinez, Inverse depth parametrization for monocular SLAM, IEEE transactions on robotics, 2008.

附：SVO相关中文博客、材料</br>
[冯兵的博客|内外兼修 <一步步完善视觉里程计>是对SVO比较完整的介绍。][fb]</br>
[白巧克力亦唯心的博客 – 程序园][white] 贺一家的博客，有几篇关于SVO的介绍。</br>
[路游侠 – 博客园][lu] 这个是谁。。。知道的回复我一下。。。</br>
[ORB-LSD-SVO比较-刘浩敏_bilibili_演讲•公开课_科技_bilibili_哔哩哔哩弹幕视频网][bilibili3] 浩敏师兄讲的一次ppt</br>


作者：半闲居士</br>
链接：[https://www.zhihu.com/question/39904950/answer/138644975](https://www.zhihu.com/question/39904950/answer/138644975)</br>
来源：知乎</br>
著作权归作者所有，转载请联系作者获得授权。


[Chris]:http://www.cforster.ch/
[github]:https://github.com/uzh-rpg/rpg_svo
[blog]:http://www.cnblogs.com/gaoxiang12/p/5689927.html
[bilibili]:http://www.bilibili.com/video/av6299156/
[bilibili2]:http://www.bilibili.com/video/av7705856/
[baidupan]:https://pan.baidu.com/s/1c2qPdle
[SVO]:http://www.cnblogs.com/luyb/p/5773691.html
[fb]:http://fengbing.net/
[white]:http://www.voidcn.com/blog/wxzxur
[lu]:http://www.cnblogs.com/luyb/
[bilibili3]:http://www.bilibili.com/video/av5934066/






