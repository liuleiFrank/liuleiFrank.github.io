---
layout: post
title: Very Deep Convolutional Networks
author: Bin Li
tags: [Deep Learning]
image: 
comments: true
published: false
---

## Programming Error Collections
在 Mac 上用 virtualenv 时引入Matplotlib会出现以下问题：
```
RuntimeError: Python is not installed as a framework. The Mac OS X backend will not be able to function correctly if Python is not installed as a framework. See the Python documentation for more information on installing Python as a framework on Mac OS X. Please either reinstall Python as a framework, or try one of the other backends. If you are Working with Matplotlib in a virtual enviroment see 'Working with Matplotlib in Virtual environments' in the Matplotlib FAQ
```

在引入matplotlib时加入以下指令可以解决：
```
import matplotlib  
matplotlib.use('TkAgg')   
import matplotlib.pyplot as plt  
```

编写好网络代码后准备运行，报如下错误：
```
Dimensions must be equal, but are 3 and 224 for 'conv1_1/Conv2D' (op: 'Conv2D') with input shapes: [?,224,224,3], [3,224,224,64].
```

从 Stack Overflow上查到一个说法是：
```
shape of input = [batch, in_height, in_width, in_channels]
shape of filter = [filter_height, filter_width, in_channels, out_channels]
```
对应的改一下位置果然就好了。


## 知识点总结
### Image preprocessing
#### 为什么要引入Image mean subtraction？
If your data is stationary (i.e., the statistics for each data dimension follow the same distribution), then you might want to consider subtracting the mean-value for each example (computed per-example).

Example: In images, this normalization has the property of removing the average brightness (intensity) of the data point. In many cases, we are not interested in the illumination conditions of the image, but more so in the content; removing the average pixel value per data point makes sense here. Note: While this method is generally used for images, one might want to take more care when applying this to color images. In particular, the stationarity property does not generally apply across pixels in different color channels.

也就是说当光线亮度变化不是很有重要的话，通过将去平均值可以减少光线的影响，然而需要注意的是如果是有颜色的数据需要注意，稳态特性不能再不同颜色通道交叉使用。

#### Why convert to grayscale from color?
对于一些边界检测等算法，不需要RGB特征，所以可以将图片灰度化，这样能减少到三通道的三分之一的计算量，很值。

#### Why normalize data before training?
一般在训练模型之前都要对数据进行标准化，用下面的式子使得所有数据具有 zero mean, unit variance（零均值，单位方差）。

$$X_{norm}={X-X_{min}\over{X_{man}-X_{min}}}$$

灰度图的$X_{min}$就是0，$X_{max}$是255，$X_{norm}$则介于0到1之间。标准化的好处有两点：
* 有的特征数据比较大，这样会使得小数值的特征很难再特征中起到作用
* 许多学习算法对标准化之后的数据能达到比较好的效果

### TensorFlow
#### 为什么在写代码时要加入tf.name_scope？
主要考虑的是变量共享，通过tf.name_scope定义了一个共享变量的scope，[详见](https://stackoverflow.com/questions/42708989/why-do-we-use-tf-name-scope)。

#### tf.truncated_normal 干嘛用?
Outputs random values from a truncated normal distribution.

The generated values follow a normal distribution with specified mean and standard deviation, except that values whose magnitude is more than 2 standard deviations from the mean are dropped and re-picked.

## 涉及的工具库总结
### python 库
#### [glob](https://docs.python.org/2/library/glob.html)
Unix style pathname pattern expansion，主要是用来匹配路径名的一个库。

####  tf.nn.avg_pool, tf.nn.max_pool, tf.nn.conv2d 中`strides`的四个参数各表示什么？
If the input tensor has 4 dimensions:  [batch, height, width, channels], then the convolution operates on a 2D window on the height, width dimensions.

`strides` determines how much the window shifts by in each of the dimensions. The typical use sets the first (the batch) and last (the depth) stride to 1.

具体可[参考](https://stackoverflow.com/questions/34642595/tensorflow-strides-argument)。

#### [batch, height, width, channel] size 要怎么算？
```python
# conv1
with tf.name_scope('conv1_1') as scope:
    kernel = weight_variable([3, 3, 3, 64])
    biases = bias_variable([64])
    output_conv1_1 = tf.nn.relu(conv2d(x, kernel) + biases, name=scope)

with tf.name_scope('conv1_2') as scope:
    kernel = weight_variable([3, 3, 64, 64])
    biases = bias_variable([64])
    output_conv1_2 = tf.nn.relu(conv2d(output_conv1_1, kernel) + biases, name=scope)
```
如上面代码片段，第一层的卷积层和第二层的width大小变化了，输入的大小是[3, 3, 3, 64]，conv_layer的设置是[1, 1, 1, 1]。

## References
1. [Tensorflow VGG16 and VGG19](https://github.com/machrisaa/tensorflow-vgg)
2. [tensorflow-vgg16-train-and-test](https://github.com/ppplinday/tensorflow-vgg16-train-and-test)
3. [TensorFlow VGG-16 pre-trained model](https://github.com/ry/tensorflow-vgg16)
4. [Data Preprocessing in UFLDL Tutorial](http://ufldl.stanford.edu/wiki/index.php/Data_Preprocessing)
5. [Image Processing Tips Example Code](https://github.com/kharikri/Image-Processing-Tips/blob/master/Image%20Processing%20Tips%20Example%20Code.ipynb)
6. [Re-implementation of VGG Network in tensorflow](https://github.com/huyng/tensorflow-vgg)
7. ✳️ [An easy implement of VGG19 with tensorflow, which has a detailed explanation.](https://github.com/hjptriplebee/VGG19_with_tensorflow)
8. [conversation of caffe vgg16 model to tensorflow](https://github.com/ry/tensorflow-vgg16)
9. ✳️ [Implementing VGG13 for MNIST dataset in TensorFlow](https://medium.com/@amir_hf8/implementing-vgg13-for-mnist-dataset-in-tensorflow-abc1460e2b93)
10. [利用卷积神经网络(VGG19)实现火灾分类(附tensorflow代码及训练集)](http://www.cnblogs.com/vipyoumay/p/7884472.html)
11. [A TensorFlow implementation of VGG networks for image classification](https://github.com/conan7882/VGG-cifar-tf)
12. [Very Deep Convolutional Networks for Large-Scale Image Recognition](https://arxiv.org/abs/1409.1556)
13. [Implementing VGG13 for MNIST dataset in TensorFlow](https://medium.com/@amir_hf8/implementing-vgg13-for-mnist-dataset-in-tensorflow-abc1460e2b93)