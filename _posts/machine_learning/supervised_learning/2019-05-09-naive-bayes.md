---
layout: post
title: Naive Bayes
subtitle: 朴素贝叶斯
author: Bin
tags: [Machine Learning]
comments: true
published: true
typora-root-url: ../../../../binlidaily.github.io
typora-copy-images-to: ../../../img/media
---

　　在整理贝叶斯分类器之前，我们先介绍一下条件概率公式，条件概率是全概率公式和贝叶斯公式的基础。

## 1. 背景知识
### 1.1 条件概率公式
　　设 A、B 是两个事件，且 $P(B)>0$，则在时间 B 发生的条件下，事件 A 发生的条件概率（conditional probability）为：

$$
P(A\vert B) = {P(AB) \over {P(B)}}
$$

　　如果 $P(A\vert B) \gt P(A)$，表示 B 的发生使得 A 发生的可能性变大了。在条件概率中，最本质的变化是**样本空间变小了**，由原来的整个样本空间缩小到了给定条件的样本空间。

<p align="center">
  <img width="500" height="" src="/img/media/15577342647670.jpg">
</p>

### 1.2 乘法公式
　　由概率公式可以推出乘法公式：

$$
P(A B)=P(B) \cdot P(A \vert B)=P(A) \cdot P(B \vert A)
$$

　　推广一下乘法公式，对于任何正整数 $n\ge 2$，当 $P(A_1 A_2 \ldots A_{n-1}) > 0$ 时有：

$$
\mathrm{P}\left(\mathrm{A}_{1} \mathrm{A}_{2} \ldots \mathrm{A}_{\mathrm{n}-1} \mathrm{A}_{\mathrm{n}}\right)=\mathrm{P}\left(\mathrm{A}_{1}\right) \mathrm{P}\left(\mathrm{A}_{2} \vert \mathrm{A}_{1}\right) \mathrm{P}\left(\mathrm{A}_{3} \vert \mathrm{A}_{1} \mathrm{A}_{2}\right) \ldots \mathrm{P}\left(\mathrm{A}_{\mathrm{n}} \vert \mathrm{A}_{1} \mathrm{A}_{2} \ldots \mathrm{A}_{\mathrm{n}-1}\right)
$$

### 1.3 贝叶斯公式
<p align="center">
  <img width="500" height="" src="/img/media/15593028139966.jpg">
</p>

　　贝叶斯公式是建立在条件概率的基础上寻找事件发生的原因（即大事件 $A$ 已经发生的条件下，分割中的小事件 $B_i$ 在 $A$ 发生的条件下的概率），设 $B_1, B_2,\dots, B_n $ 是样本空间 $S$ 的一个划分，则对任一事件 $A$（$P(A)>0$）有：

$$
P\left(B_{i} | A\right)=\frac{P\left(AB_{i}\right)}{\sum_{j=1}^{n} P\left(AB_{j}\right)} =\frac{P\left(B_{i}\right) P\left(A | B_{i}\right)}{\sum_{j=1}^{n} P\left(B_{j}\right) P\left(A | B_{j}\right)}
$$

　　这就是贝叶斯公式（Bayes formula），$B_i$ 常被视为导致试验结果 $A$ 发生的“原因”，$P(B_i)$（$i=1,2,\dots, n$）表示各种原因发生的可能性大小，故称先验概率（权重）；$P(B_i\vert A)$（$i=1,2,\dots, n$）则反映当试验产生了结果 $A$ 之后，再对各种原因概率的新认识，故称后验概率。具体每一个部分的解释可以[参考](https://www.probabilisticworld.com/anatomy-bayes-theorem/)。

　　还有一种比较容易的解释形式，贝叶斯定理的公式如下：

$$
P(\text { Hypothesis } | \text { Observation })=\frac{\text { P(Observation } | \text { Hypothesis) } \times P(\text { Hypothesis) }}{P(\text { Observation })}
$$

　　假如说所有的数据中有四种 Hypothesis（也就是四种类），那么在观察之前的空间如下图所示，这是假设情况下的，也就是**先验**的内容：
![](/img/media/15662835074348.jpg)

　　然后我们观察一批数据，因为现实中不可能把所有数据都拿来算概率，只能采样一部分数据作为观察样本，也就是下面图中暗色的框框：
![](/img/media/15662834571040.jpg)

　　然后我们只看观察的数据，那么这个就是**后验**的内容：
![](/img/media/15662841983672.jpg)

　　当然对于观察数据还有这样的联合概率加和的关系存在，比较好理解：

![](/img/media/15662842343320.jpg)

　　The likelihood reads as “the probability of the observation, given that the hypothesis is true”.
## 2. 朴素贝叶斯分类器
　　为了方便讨论，这里用更加偏向机器学习的表示方式重写贝叶斯公式：

$$
P(c \vert \boldsymbol{x})=\frac{P(c)P(\boldsymbol{x}|c)}{\sum_c P(c)P(\boldsymbol{x}|c)}=\frac{P(c) P(\boldsymbol{x} \vert c)}{P(\boldsymbol{x})}
$$

　　其中 $P(x\vert c)$ 是类条件概率（或者叫似然性），表示的是给定某个类后，它具有某种特性（$x$ 的一行特征，不同的 $x_i$ 取到的概率不同）的概率是多少；$P(c)$ 是先验概率，表明在测量之前各个结果的取值概率。$P(\boldsymbol{x})$ 是规范化条件，使得概率之和为 1。记贝叶斯定理口诀：$后验=先验\times 似然性$。

　　用上述的贝叶斯公式来估计后验概率的主要难度在于类条件概率是**样本所有属性上的联合概率**，难以从现有有限的训练样本直接估计得到。举个例子，比如西瓜书中说的“好瓜”这个类别，那么在“好瓜”的前提条件下，要考虑一个瓜所有的属性特征，即要考虑色泽是青绿或乌黑的概率，要考虑根蒂是蜷缩或硬挺的概率，共要考虑 8 个特征的联合概率。

　　为了避开这个麻烦的障碍，我们就假设所有属性是相互独立的，即每个属性独立地对分类结果产生影响，因为有这个相对较强的假设，所以被称为“朴素”贝叶斯分类器。有了这个假设后我们在求类条件概率时所需要计算所有属性上的联合概率，直接就可以用相乘得到结果了，因为其间是相互独立的啊，真是很方便是不是。😆

　　基于属性条件独立性假设，贝叶斯公式又可以进一步写成：

$$
P(c \vert \boldsymbol{x})=\frac{P(c) P(\boldsymbol{x} \vert c)}{P(\boldsymbol{x})}=\frac{P(c)}{P(\boldsymbol{x})} \prod_{i=1}^{d} P\left(x_{i} \vert c\right)
$$

　　其中 $d$ 为属性的个数，$x_i$ 为 $\boldsymbol{x}$ 在第 $i$ 个属性上的取值。由于 $P(\boldsymbol{x})$ 对于所有类别来说都相同，所以就能得到朴素贝叶斯分类器的表达式：

$$
h_{n b}(\boldsymbol{x})=\underset{c \in \mathcal{Y}}{\arg \max } P(c) \prod_{i=1}^{d} P\left(x_{i} | c\right)
$$

　　计算所有类别下的结果，选出概率最大的作为分类结果。那么我们只需要对公式中所有变量确定求解办法就能用来分类了，首先是先验概率分布 $P(c)$，我们令 $D_c$ 表示为 $D$ 中第 $c$ 类样本组成的集合，若有充足的独立同分布样本，则可以较容易地估计出类先验概率：

$$
P(c)=\frac{\left\vert D_{c}\right\vert}{\vert D\vert}
$$

　　对于类条件概率的估计分两类，因为特征值可能是离散的也可能是连续的。对于离散属性而言，记 $D_{c,x_i}$ 表示 $D_c$ 中在第 $i$ 个属性上取值为 $x_i$ 的样本组成的集合，那么类条件概率 $P(x_i \vert c)$ 可估计为：

$$
P\left(x_{i} \vert c\right)=\frac{\left|D_{c, x_{i}}\right|}{\left|D_{c}\right|}
$$

　　对于连续属性而言考虑其概率密度函数，假设 $p\left(x_{i} \vert c\right) \sim \mathcal{N}\left(\mu_{c, i}, \sigma_{c, i}^{2}\right)$，其中 $\mu_{c, i}$ 和 $\sigma_{c, i}^{2}$ 分别是第 $c$ 类样本在第 $i$ 个属性上取值的均值和方差，则有：

$$
p\left(x_{i} | c\right)=\frac{1}{\sqrt{2 \pi} \sigma_{c, i}} \exp \left(-\frac{\left(x_{i}-\mu_{c, i}\right)^{2}}{2 \sigma_{c, i}^{2}}\right)
$$

　　至此就可以用贝叶斯分类器的表达式进行分类了。


　　值得注意的是，在利用朴素贝叶斯进行**预测**分类的时候，如果某个离散特征的属性值从来没有出现过，那么其对应的条件概率为 ０，这样会使得连乘的结果也为 0，那么可以通过拉普拉斯平滑（Laplace Smoothing）来优化:

$$
\hat{P}(c)=\frac{\left|D_{c}\right|+1}{|D|+N}
$$

$$
\hat{P}\left(x_{i} | c\right)=\frac{\left|D_{c, x_{i}}\right|+1}{\left|D_{c}\right|+N_{i}}
$$

　　其中 $N$ 是总的类别数，$N_i$ 是第 $i$ 个属性可能的取值，这样结果使得各个部分的概率累加和还是 1。

### 优缺点
贝叶斯**优点**：
1. 对小规模数据表现很好，能处理多分类，适合增量式训练（超内存时，可以一批一批训练）。
2. 对缺失数据不敏感，算法较简单，常用于文本分类。

贝叶斯**缺点**：
1. 假设的是各个属性间相互独立，实际情况必然很难达到这样的条件，分类效果不一定很好。
2. 需要知道先验概率，先验概率很多时候基于假设，假设的先验模型不一定很好致使效果不佳。
3. 由于是通过先验和数据来决定后验的概率从而决定分类，所以分类决策存在一定的误差率。
4. 对输入数据的表达形式很敏感。

### 什么时候用朴素贝叶斯，什么时候用正态贝叶斯？
一般我们都用朴素贝叶斯，因为它计算简单。除非特征向量维数不高、特征分量之间存在严重的相关性我们才用正态贝叶斯，如果特征向量是n维的，正态贝叶斯在训练时需要计算n阶矩阵的逆矩阵和行列式，这非常耗时。

## References
1. [贝叶斯公式](http://www.cnblogs.com/elaron/archive/2012/10/25/2739236.html)
2. [【概率论与数理统计】全概率公式和贝叶斯公式](https://www.cnblogs.com/Belter/p/5923828.html)
3. [朴素贝叶斯算法原理小结](https://www.cnblogs.com/pinard/p/6069267.html)
4. [Naive-Bayes-Classifier](https://github.com/lining0806/Naive-Bayes-Classifier)