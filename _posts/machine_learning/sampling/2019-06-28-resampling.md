---
layout: post
title: Resampling
subtitle: 重采样
author: Bin
tags: [Machine Learning]
comments: true
published: true
typora-root-url: ../../../../binlidaily.github.io
typora-copy-images-to: ../../../img/media
---

　　采样，顾名思义，就是从特定的概率分布中抽取相应样本点的过程。采样在机器学习中有着非常重要的应用：它可以将复杂的分布简化为离散的样本点；可以用[重采样]()对样本集进行调整以更好地适应后期的模型学习；可以用于随机模拟以进行复杂模型的近似求解或推理。另外，采样在数据可视化方面也有很多应用，可以帮助人们快速、直观地了解数据的结构和特性。

　　对于一些简单的分布，如均匀分布、高斯分布等，很多编程语言里面都有直接的采样函数。然而，即使是这些简单分布，其采样过程也并不是显而易见的，仍需要精心设计。对于比较复杂的分布，往往并没有直接的采样函数可供调用，这时就需要其他更加复杂的采样方法。因此，对采样方法的深入理解是很有必要的。

　　采样本质上是对随机现象的模拟，根据给定的概率分布，来模拟产生一个对应的随机事件。采样可以让人们对随机事件及其产生过程有更直观的认识。例如，通过对二项分布的采样，可以模拟“抛硬币出现正面还是反面”这个随机事件，进而模拟产生一个多次抛硬币出现的结果序列，或者计算多次抛硬币后出现正面的频率。

　　另一方面，采样得到的样本集也可以看作是一种非参数模型，即用较少量的样本点（经验分布）来近似总体分布，并刻画总体分布中的不确定性。从这个角度来说，采样其实也是一种信息降维，可以起到简化问题的作用。例如，在训练机器学习模型时，一般想要优化的是模型在总体分布上的期望损失（期望风险），但总体分布可能包含无穷多个样本点，要在训练时全部用上几乎是不可能的，采集和存储样本的代价也非常大。因此，一般采用总体分布的一个样本集来作为总体分布的近似，称之为训练集，训练模型的时候是最小化模型在训练集上损失函数（经验风险）。同理，在评估模型时，也是看模型在另外一个样本集（测试集）上的效果。这种信息降维的特性，使得采样在数据可视化方面也有很多应用，它可以帮助人们快速、直观地了解总体分布中数据的结构和特性。

[ ] 总结采样的作用、在机器学习中的应用

在机器学习中应用：
1. 估计偏差、方差

对于不同分布的采样：
1. 对均匀分布的采样
2. 对高斯分布的采样
3. 对贝叶斯网络的采样
4. 不均衡样本集的重采样

常见采样的方法：
1. 逆变换采样
2. 拒绝采样
3. 重要性采样
4. 马尔科夫蒙特卡洛采样法

## References
1. 《百面机器学习》