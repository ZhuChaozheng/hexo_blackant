---
title: Nature machine intelligence
date: 2019-03-16 10:32:14
tags:
---

#内容分类
<!--more-->
##一、理论综述
####(Learnability can be undecidable、)
###可学习性是不可决定的

  The mathematical foundations of machine learning play a key role in the development of the field. They improve our understanding and provide tools for designing new learning paradigms. The advantages of mathematics, however, sometimes come with a cost. Gödel and Cohen showed, in a nutshell, that not everything is provable. Here we show that machine learning shares this fate. We describe simple scenarios where learnability cannot be proved nor refuted using the standard axioms of mathematics. Our proof is based on the fact the continuum hypothesis cannot be proved nor refuted. We show that, in some cases, a solution to the ‘estimating the maximum’ problem is equivalent to the continuum hypothesis. The main idea is to prove an equivalence between learnability and compression.
***


##二、新技术、方法（提升效率，拓宽功能方面）
####Differential game theory for versatile physical human–robot interaction
###多用途人机交互的微分博弈论

The last decades have seen a surge of robots working in contact with humans. However, until now these contact robots have made little use of the opportunities offered by physical interaction and lack a systematic methodology to produce versatile behaviours. Here, we develop an interactive robot controller able to understand the control strategy of the human user and react optimally to their movements. We demonstrate that combining an observer with a differential game theory controller can induce a stable interaction between the two partners, precisely identify each other’s control law, and allow them to successfully perform the task with minimum effort. Simulations and experiments with human subjects demonstrate these properties and illustrate how this controller can induce different representative interaction strategies.

***

####Long short-term memory networks in memristor crossbar arrays
###忆阻交叉棒阵列中的长、短期记忆网络

Recent breakthroughs in recurrent deep neural networks with long short-term memory (LSTM) units have led to major advances in artificial intelligence. However, state-of-the-art LSTM models with significantly increased complexity and a large number of parameters have a bottleneck in computing power resulting from both limited memory capacity and limited data communication bandwidth. Here we demonstrate experimentally that the synaptic weights shared in different time steps in an LSTM can be implemented with a memristor crossbar array, which has a small circuit footprint, can store a large number of parameters and offers in-memory computing capability that contributes to circumventing the ‘von Neumann bottleneck’. We illustrate the capability of our crossbar system as a core component in solving real-world problems in regression and classification, which shows that memristor LSTM is a promising low-power and low-latency hardware platform for edge inference.
***
最近，在具有长短期记忆单元的深度神经网络(LSTM)方面的突破使得人工智能取得了重大进展。然而，由于有限的内存容量和有限的数据通信带宽，目前最先进的LSTM模型由于其复杂性和大量的参数而在计算能力方面存在瓶颈。在这里，我们通过实验证明，LSTM中不同时间步长共享的突触权值可以通过忆阻交叉棒阵列实现，该阵列具有较小的电路占用空间，可以存储大量参数，并提供内存计算能力，有助于绕过“冯•诺伊曼瓶颈”。我们举例说明了我们的交叉杆系统作为一个核心组件在解决实际的回归和分类问题中的能力，这表明忆阻LSTM是一个很有前途的低功耗和低延迟的边缘推理硬件平台。

####Causal deconvolution by algorithmic generative models
###因果反褶积算法生成模型

Complex behaviour emerges from interactions between objects produced by different generating mechanisms. Yet to decode their causal origin(s) from observations remains one of the most fundamental challenges in science. Here we introduce a universal, unsupervised and parameter-free model-oriented approach, based on the seminal concept and the first principles of algorithmic probability, to decompose an observation into its most likely algorithmic generative models. Our approach uses a perturbation-based causal calculus to infer model representations. We demonstrate its ability to deconvolve interacting mechanisms regardless of whether the resultant objects are bit strings, space–time evolution diagrams, images or networks. Although this is mostly a conceptual contribution and an algorithmic framework, we also provide numerical evidence evaluating the ability of our methods to extract models from data produced by discrete dynamical systems such as cellular automata and complex networks. We think that these separating techniques can contribute to tackling the challenge of causation, thus complementing statistically oriented approaches.



####Training deep neural networks for binary communication with the Whetstone method
###用磨刀石法训练二进制通信的深度神经网络

The computational cost of deep neural networks presents challenges to broadly deploying these algorithms. Low-power and embedded neuromorphic processors offer potentially dramatic performance-per-watt improvements over traditional processors. However, programming these brain-inspired platforms generally requires platform-specific expertise. It is therefore difficult to achieve state-of-the-art performance on these platforms, limiting their applicability. Here we present Whetstone, a method to bridge this gap by converting deep neural networks to have discrete, binary communication. During the training process, the activation function at each layer is progressively sharpened towards a threshold activation, with limited loss in performance. Whetstone sharpened networks do not require a rate code or other spike-based coding scheme, thus producing networks comparable in timing and size to conventional artificial neural networks. We demonstrate Whetstone on a number of architectures and tasks such as image classification, autoencoders and semantic segmentation. Whetstone is currently implemented within the Keras wrapper for TensorFlow and is widely extendable.

***



##三、介绍具体应用（并非普适性技术，有针对性）
####Deep-learning cardiac motion analysis for human survival prediction
###心脏运动深度学习分析用于人类生存预测

Motion analysis is used in computer vision to understand the behaviour of moving objects in sequences of images. Optimizing the interpretation of dynamic biological systems requires accurate and precise motion tracking as well as efficient representations of high-dimensional motion trajectories so that these can be used for prediction tasks. Here we use image sequences of the heart, acquired using cardiac magnetic resonance imaging, to create time-resolved three-dimensional segmentations using a fully convolutional network trained on anatomical shape priors. This dense motion model formed the input to a supervised denoising autoencoder (4Dsurvival), which is a hybrid network consisting of an autoencoder that learns a task-specific latent code representation trained on observed outcome data, yielding a latent representation optimized for survival prediction. To handle right-censored survival outcomes, our network used a Cox partial likelihood loss function. In a study of 302 patients, the predictive accuracy (quantified by Harrell’s C-index) was significantly higher (P = 0.0012) for our model C = 0.75 (95% CI: 0.70–0.79) than the human benchmark of C = 0.59 (95% CI: 0.53–0.65). This work demonstrates how a complex computer vision task using high-dimensional medical image data can efficiently predict human survival.
***



####Feedback GAN for DNA optimizes protein functions
###DNA反馈GAN优化蛋白质功能

Generative adversarial networks (GANs) represent an attractive and novel approach to generate realistic data, such as genes, proteins or drugs, in synthetic biology. Here, we apply GANs to generate synthetic DNA sequences encoding for proteins of variable length. We propose a novel feedback-loop architecture, feedback GAN (FBGAN), to optimize the synthetic gene sequences for desired properties using an external function analyser. The proposed architecture also has the advantage that the analyser does not need to be differentiable. We apply the feedback-loop mechanism to two examples: generating synthetic genes coding for antimicrobial peptides, and optimizing synthetic genes for the secondary structure of their resulting peptides. A suite of metrics, calculated in silico, demonstrates that the GAN-generated proteins have desirable biophysical properties. The FBGAN architecture can also be used to optimize GAN-generated data points for useful properties in domains beyond genomics.

***


###An integrated iterative annotation technique for easing neural network training in medical image analysis
###一种用于医学图像分析中简化神经网络训练的集成迭代标注技术

Neural networks promise to bring robust, quantitative analysis to medical fields. However, their adoption is limited by the technicalities of training these networks and the required volume and quality of human-generated annotations. To address this gap in the field of pathology, we have created an intuitive interface for data annotation and the display of neural network predictions within a commonly used digital pathology whole-slide viewer. This strategy used a ‘human-in-the-loop’ to reduce the annotation burden. We demonstrate that segmentation of human and mouse renal micro compartments is repeatedly improved when humans interact with automatically generated annotations throughout the training process. Finally, to show the adaptability of this technique to other medical imaging fields, we demonstrate its ability to iteratively segment human prostate glands from radiology imaging data.

***

##内容比例

* 理论：1
* 方法：4
+ 应用：3

##预计趋势与评价：
由于本期刊是19年1月起发行，因而考虑了两月的前言；
*整理可知*，《自然：机器智能》将致力于将不同领域融合在一起，在人工智能、机器人、认知科学和机器学习等领域打造新的合作伙伴，进一步开发出对人类具有启发和用途的智能机器愿景。大致有*三个主题*是我们将首先关注的:
构建智能机器的算法和硬件的工程和研究;
机器智能(如深度学习系统)在其他领域(如物理、生物和医疗保健)的特定领域和主题的应用;最后研究机器智能对社会、工业和科学的影响。
*其次是*文章允许作者在出版物中加上自己的arxiv与biorxiv的链接以供所有读者观看。（arxiv与biorxiv完全免费开放）；

其中我们投稿主要是冲着article去的，因而基本不需要关注三大主题中的第三个，以上的整理由于太少只能作为参考，整体来看首先是综述类文章比起主刊*少得多*，然后绝大多数是方法类、应用类，其中方法类偏向于人工智能，神经网络等，应用偏向于医学、生物类应用。

此外，本期刊由于相对高昂的费用，以及封闭的订阅式（难以直接在网上查看全文且订阅费用也高昂，与其他刊登计算机类文章的期刊不同）遭到了很多知名学者的抵制，他们认为这与计算机类的开放风格矛盾，个人进行投稿务必要考虑到经济的因素。
至少现在来看，内容上感觉偏向于理论，即便是应用也是技术而不是机器实体，与硬件关系不是很大。
以上是目前已刊登的全部文章，相关的文章有：

####Deep-learning cardiac motion analysis for human survival prediction
###心脏运动深度学习分析用于人类生存预测
Motion analysis is used in computer vision to understand the behaviour of moving objects in sequences of images. Optimizing the interpretation of dynamic biological systems requires accurate and precise motion tracking as well as efficient representations of high-dimensional motion trajectories so that these can be used for prediction tasks. Here we use image sequences of the heart, acquired using cardiac magnetic resonance imaging, to create time-resolved three-dimensional segmentations using a fully convolutional network trained on anatomical shape priors. This dense motion model formed the input to a supervised denoising autoencoder (4Dsurvival), which is a hybrid network consisting of an autoencoder that learns a task-specific latent code representation trained on observed outcome data, yielding a latent representation optimized for survival prediction. To handle right-censored survival outcomes, our network used a Cox partial likelihood loss function. In a study of 302 patients, the predictive accuracy (quantified by Harrell’s C-index) was significantly higher (P = 0.0012) for our model C = 0.75 (95% CI: 0.70–0.79) than the human benchmark of C = 0.59 (95% CI: 0.53–0.65). This work demonstrates how a complex computer vision task using high-dimensional medical image data can efficiently predict human survival.

***


*计算机视觉相关且比较创新的应用也许可以投此刊*





