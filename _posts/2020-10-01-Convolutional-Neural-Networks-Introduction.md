---
title: 'Convolutional Neural Networks Introduction'
date: 2020-10-01
tags:
  - Convolution Neural Network
  - CNN
---

# Why Convolutions

- **Parameter sharing**: a feature detector (such as a vertical edge detector) that’s useful in one part of the image is probably useful in another part of the image.
- **Sparsity of connections**: in each layer, each output value depends only on small number of inputs.

# Convolution Operation

## Basic Convolution Operation

**Step 1**: overlay the filter to the input, perform element wise multiplication, and add the result.

![Convolution Operation 1](https://indoml.files.wordpress.com/2018/03/convolution-operation-14.png?w=736)



**Step 2**: move the overlay right one position (or according to the **stride** setting), and do the same calculation above to get the next result. And so on.

![Convolution Operation 2](https://indoml.files.wordpress.com/2018/03/convolution-operation-24.png?w=726&h=307)

The total number of multiplications to calculate the result above is (4 x 4) x (3 x 3) = 144.

## Stride

Stride governs how many cells the filter is moved in the input to calculate the next cell in the result.

![Stride](https://indoml.files.wordpress.com/2018/03/stride.png?w=736)Filter with stride (s) = 2

The total number of multiplications to calculate the result above is (2 x 2) x (3 x 3) = 36.

## Padding

Padding has the following benefits:

1. It allows us to use a CONV layer without necessarily shrinking the height and width of the volumes. This is important for building deeper networks, since otherwise the height/width would shrink as we go to deeper layers.
2. It helps us keep more of the information at the border of an image. Without padding, very few values at the next layer would be affected by pixels as the edges of an image.



![Padding](https://indoml.files.wordpress.com/2018/03/padding.png?w=618&h=303)

Notice the dimension of the result has changed due to padding. See the following section on how to calculate output dimension.

Some padding terminologies:

- “**valid**” padding: no padding
- “**same**” padding: padding so that the output dimension is the same as the input

## Convolution Operation on Volume

When the input has more than one channels (e.g. an RGB image), the filter should have matching number of channels. To calculate one output cell, perform convolution on each matching channel, then add the result together.



![Convolution Operation on Volume](https://indoml.files.wordpress.com/2018/03/convolution-operation-on-volume5.png?w=736)

 

The total number of multiplications to calculate the result is (4 x 4) x (3 x 3 x 3) = 432.

## Convolution Operation with Multiple Filters

Multiple filters can be used in a convolution layer to detect multiple features. The output of the layer then will have the same number of channels as the number of filters in the layer.

![Convolution with Multiple Filters](https://indoml.files.wordpress.com/2018/03/convolution-with-multiple-filters2.png?w=736)



The total number of multiplications to calculate the result is (4 x 4 x 2) x (3 x 3 x 3) = 864.

## 1 x 1 Convolution

This is convolution with 1 x 1 filter. The effect is to flatten or “merge” channels together, which can save computations later in the network:

![1x1 Convolution](https://indoml.files.wordpress.com/2018/03/1x1-convolution1.png?w=677&h=359)

~

# One Convolution Layer

Finally to make up a convolution layer, a bias (ϵ R) is added and an activation function such as **ReLU** or **tanh** is applied.

![One Convolution Layer](https://indoml.files.wordpress.com/2018/03/one-convolution-layer1.png?w=736)



# Shorthand Representation

This simpler representation will be used from now on to represent one convolutional layer:

![CNN Simpler Notation 2](https://indoml.files.wordpress.com/2018/03/cnn-simpler-notation-2.jpg?w=404&h=207)

# Sample Complete Network

This is a sample network with three convolution layers. At the end of the network, the output of the convolution layer is flattened and is connected to a logistic regression or a softmax output layer.

![CNN Complete](https://indoml.files.wordpress.com/2018/03/cnn-complete2.png?w=736)

# Pooling Layer

Pooling layer is used to reduce the size of the representations and to speed up calculations, as well as to make some of the features it detects a bit more robust.

Sample types of pooling are **max pooling** and **avg pooling**, but these days max pooling is more common.

![Pooling Layer](https://indoml.files.wordpress.com/2018/03/pooling-layer3.png?w=648&h=180)

Interesting properties of pooling layer:

- it has hyper-parameters:
  - **size** (***f***)
  - **stride** (***s***)
  - **type** (max or avg)
- but it doesn’t have parameter; there’s nothing for gradient descent to learn

When done on input with multiple channels, pooling reduces the height and width (W and H) but keeps C unchanged:

![Pooling on Volume](https://indoml.files.wordpress.com/2018/03/pooling-on-volume1.png?w=441&h=286)



~

# Well Known Architectures

## Classic Network: LeNet – 5

One example of classic networks is LeNet-5, from [*Gradient-Based Learning Applied to Document Recognition*](http://yann.lecun.com/exdb/publis/pdf/lecun-98.pdf) paper by Y. Lecun, L. Bottou, Y. Bengio and P. Haffner (1988):

![LeNet - 5.png](https://indoml.files.wordpress.com/2018/03/lenet-52.png?w=736)

- Number of parameters: ~ 60 thousands.

## Classic Network: AlexNet

AlexNet is another classic CNN architecture from [*ImageNet Classification with Deep Convolutional Neural Networks*](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf) paper by Alex Krizhevsky, Geoffrey Hinton, and Ilya Sutskever (2012).

![AlexNet](https://indoml.files.wordpress.com/2018/03/alexnet.png?w=736)

- Number of parameters: ~ 60 millions.

## Classic Network: VGG-16

VGG-16 from [*Very Deep Convolutional Networks for Large-Scale Image Recognition*](https://arxiv.org/abs/1409.1556) paper by Karen Simonyan and Andrew Zisserman (2014). The number 16 refers to the fact that the network has 16 trainable layers (i.e. layers that have weights).

![vgg16](https://indoml.files.wordpress.com/2018/03/vgg16.png?w=736)(image from [blog.heuritech.com](https://blog.heuritech.com/2016/02/29/a-brief-report-of-the-heuritech-deep-learning-meetup-5/vgg16/))

- Number of parameters: ~ 138 millions.
- The strength is in the simplicity: the dimension is halved and the depth is increased on every step (or stack of layers)

## ResNet

The problem with deeper neural networks are they are harder to train and once the number of layers reach certain number, the training error starts to raise again. Deep networks are also harder to train due to exploding and vanishing gradients problem. ResNet (Residual Network), proposed by He at all in [*Deep Residual Learning for Image Recognition paper* ](https://arxiv.org/abs/1512.03385)(2015), solves these problems by implementing skip connection where output from one layer is fed to layer deeper in the network:



![ResNet Skip Connection](https://indoml.files.wordpress.com/2018/03/resnet-skip-connection.png?w=736)

In the image above, the skip connection is depicted by the red line. The activation ***a[l+2]\*** is then calculated as:

***z[l+2]\* = \*W[l+2]\* \*a[l+1]\* + \*b[l+2]\*** 

***a[l+2]\* = \*g[l+2]\*(\*z[l+2]\* +\*a[l]\*)** 

The advantages of ResNets are:

- performance doesn’t degrade with very deep network
- cheaper to compute
- ability to train very very deep network

ResNet works because:

- identify function is easy for residual block to learn
- using a skip-connection helps the gradient to back-propagate and thus helps you to train deeper networks

![resNet](https://indoml.files.wordpress.com/2018/03/resnet.jpg?w=736)Comparison of 34 layers ResNet with plain network (image from [euler.stat.yale.edu](http://euler.stat.yale.edu/~tba3/stat665/lectures/lec18/img/resNet.jpg))

## Inception

The motivation of the inception network is, rather than requiring us to pick the filter size manually, let the network decide what is best to put in a layer. We give it choices and hopefully it will pick up what is best to use in that layer:



![Inception Motivation](https://indoml.files.wordpress.com/2018/03/inception-motivation.png?w=736)

The problem with the above network is computation cost (e.g. for the 5 x 5 filter only, the computation cost is (28 x 28 x 32) x (5 x 5 x 192) = ~ 120 millions).

Using 1 x 1 convolution will reduce the computation to about 1/10 of that. With this idea, an inception module will look like this:

![Inception Module](https://indoml.files.wordpress.com/2018/03/inception-module1.png?w=736)

Below is an inception network called **GoogLeNet**, described in [*Going Deeper with Convolutions paper*](https://arxiv.org/abs/1409.4842) by Szegedy et all (2014), which has 9 inception modules:

![googlenet_diagram](https://indoml.files.wordpress.com/2018/03/googlenet_diagram1.png?w=736)GoogLeNet architecture (image [source](http://joelouismarino.github.io/images/blog_images/blog_googlenet_keras/googlenet_diagram.png))



[https://indoml.com/2018/03/07/student-notes-convolutional-neural-networks-cnn-introduction/](https://indoml.com/2018/03/07/student-notes-convolutional-neural-networks-cnn-introduction/)