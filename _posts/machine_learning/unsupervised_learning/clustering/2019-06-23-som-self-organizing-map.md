---
layout: post
title: Gaussian Mixed Model
subtitle: 高斯混合模型
author: Bin Li
tags: [Machine Learning]
image: 
comments: true
published: true
---

　　高斯混合模型假设每个簇的数据都是符合高斯分布（又叫正态分布）的，当前数据呈现的分布就是各个簇的高斯分布叠加在一起的结果。

<p align="center">
  <img width="" height="" src="/img/media/15620491807297.jpg">
</p>

　　上子图是只用一个高斯分布来拟合图中的数据，其中椭圆表示高斯分布的二倍标准差所对应的椭圆。只管来看数据明显分为两簇，所以下子图是利用了两个高斯分布的叠加来拟合得到的结果。这就引出了了高斯混合模型，即用多个高斯分布函数的线形组合来对数据分布进行拟合。理论上，高斯混合模型可以拟合出任意类型的分布。

　　高斯混合模型的**核心思想**是，假设数据可以看作从多个高斯分布中生成出来的。在该假设下，每个单独的分模型都是标准高斯模型，其均值 $\mu_i$ 和方差 $\Sigma_i$ 是待估计的参数。此外，每个分模型都还有一个参数 $\pi$，可以理解为权重或生成数据的概率。高斯混合模型的公式为

$$
p(x)=\sum_{i=1}^{K} \pi_{i} N\left(x | \mu_{i}, \Sigma_{i}\right)
$$

　　高斯混合模型是一个生成式模型，数据的生成过程可以看成首先按照权重选择到一个高斯分布的分模型，然后再根据该高斯模型的分布生成出一个数据点，循环生成所有点即可。但是，通常我们并不能直接得到高斯混合模型的参数，而是观察到了一系列数据点，给出一个类别的数量K后，希望求得最佳的K个高斯分模型。因此，高斯混合模型的计算，便成了最佳的均值μ，方差Σ、权重π的寻找，这类问题通常通过最大似然估计来求解。遗憾的是，此问题中直接使用最大似然估计，得到的是一个复杂的非凸函数，目标函数是和的对数，难以展开和对其求偏导。

　　在这种情况下，可以用上一节已经介绍过的EM算法框架来求解该优化问题。EM 算法是在最大化目标函数时，先固定一个变量使整体函数变为凸优化函数，求导得到最值，然后利用最优参数更新被固定的变量，进入下一个循环。具体到高斯混合模型的求解，EM 算法的迭代过程如下。

　　首先，初始随机选择各参数的值。然后，重复下述两步，直到收敛。

（1）E步骤。根据当前的参数，计算每个点由某个分模型生成的概率。

（2）M步骤。使用E步骤估计出的概率，来改进每个分模型的均值，方差和权重。

　　也就是说，我们并不知道最佳的 K 个高斯分布的各自 3 个参数，也不知道每个数据点究竟是哪个高斯分布生成的。所以每次循环时，先固定当前的高斯分布不变，获得每个数据点由各个高斯分布生成的概率。然后固定该生成概率不变，根据数据点和生成概率，获得一个组更佳的高斯分布。循环往复，直到参数的不再变化，或者变化非常小时，便得到了比较合理的一组高斯分布。

高斯混合模型与 K 均值算法的相同点是：
* 它们都是可用于聚类的算法；
* 都需要指定 K 值；
* 都是使用 EM 算法来求解；
* 都往往只能收敛于局部最优。

而它相比于 K 均值算法的优点是：
* 可以给出一个样本属于某类的概率是多少；
* 不仅仅可以用于聚类，还可以用于概率密度的估计；
* 并且可以用于生成新的样本点。

## References
1. 《百面机器学习》