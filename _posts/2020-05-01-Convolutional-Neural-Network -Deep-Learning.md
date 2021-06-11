---
title: 'Convolutional Neural Network | Deep Learning'
date: 2021-06-04
tags:
  - Convolution Neural Network
  - CNN
---



Convolution Neural Network is one among various types of Deep Learning Neural Networks. CNN is very powerful and widely used in image classification, image recognition, computer vision etc.

## What is CNN ?

Convolution Neural Network has input layer, output layer, many hidden layers and millions of parameters that have the ability to learn complex objects and patterns. It sub-samples the given input by convolution and pooling processes and is subjected to activation function, where all of these are the hidden layers which are partially connected and at last end is the fully connected layer that results in the output layer. The output retains the original shape similar to input image dimensions.

### Convolution

Convolution is the process involving combination of two functions that produces the other function as a result. In CNN’s, the input image is subjected to convolution with use of filters that produces a **Feature map**.

### Filters / Kernels

Filters are randomly generated vectors in the network consisting of weights and biases. The same weights and bias are shared among various neurons in CNN instead of unique weights and bias for each neuron. Many filters can be generated where every filter captures unique feature from input. Filters are also referred as **Kernels**.

------

## Convolution Layer

Convolutions occur in convolution layer which are the building blocks of CNN. This layer generally has :

- Input vectors (*Image*)
- Filters (*Feature Detector*)
- Output vectors (*Feature map*)

**Input Image** x **Feature Detector** = **Feature Map**


This layer identify and extract best features/patterns from input image and preserves the generic information into a matrix. Matrix representation of the input image is multiplied element-wise with filters and summed up to produce a feature map, which is the same as *dot product* between combination of vectors.

*Convolution involves the following important features :*

#### **Local connectivity :**

Where each neural is connected only to a subset of input image (unlike a neural network where all neurons are fully connected). In CNN, a certain dimension of filter is chosen, which slides over these subsets of input data. Multiple filters are present in CNN where each filter moves over entire image and learns different portions of input image.

#### **Parameter Sharing** **:**

Is sharing of weights by all neurons in a particular feature map. All of them share same amounts of weight hence called parameter sharing.

### Batch Normalization

Batch normalization is generally done in between convolution and activation(ReLU) layers. It normalizes the inputs at each layer, reduces internal co-variate shift(change in the distribution of network activations) and is a method to regularize a convolutional network.
Batch normalizing allows higher learning rates that can reduce training time and gives better performance. It allows learning at each layer by itself without being more dependent on other layers. Dropout which is also a regularizing technique, is less effective to regularize convolution layers.

### Padding and Stride

Padding and Stride influence how convolution operation is performed. Padding and stride can be used to alter the dimensions(height and width) of input/output vectors either by increasing or decreasing.

**Padding** is used to make dimension of output equal to input by *adding zeros to the input frame of matrix*. Padding allows more spaces for kernel to cover image and is accurate for analysis of images. Due to padding, information on the borders of images are also preserved similarly as at the center of image.

![img](https://i0.wp.com/developersbreach.com/wp-content/uploads/2020/08/padding_item_banner-edited-1.png?w=760&ssl=1)

**Stride** controls how filter convolves over input i.e., the *number of pixels shifts over the input matrix*. If stride is set to 1, filter moves across 1 pixel at a time and if stride is 2, filter moves 2 pixels at a time. More the value of stride, smaller will be the resulting output and vice versa.

![img](https://i0.wp.com/developersbreach.com/wp-content/uploads/2020/08/Stride_item_banner.png?w=760&ssl=1)

## ReLU (Rectified Linear Unit) Layer

ReLU is computed after convolution. It is most commonly deployed activation function that allows the neural network to account for non-linear relationships. In a given matrix (x), ReLU sets all negative values to zero and all other values remains constant. It is mathematically represented as :

**y = max(0, x)**


***For example :\*** In a given matrix(M), **M**= **[ [** -3, 19, 5 **]**, **[** 7, -6, 12 **]**, **[** 4, -8, 17 **] ]**
ReLU converts it as **[ [** 0, 19, 5 **]**, **[** 7, 0, 12 **]**, **[** 4, 0, 17 **] ]**.

TanH, Sigmoid are also some other activation functions, but ReLU is used more often as it works fast, allows the network to converge very quickly and is computationally efficient.

------



## Pooling / Sub-sampling Layer

Next, there’s a pooling layer. Pooling layer operates on each feature map independently. This reduces resolution of the feature map by reducing height and width of features maps, but retains features of the map required for classification. This is called ***Down-sampling**.*

Pooling can be done in following ways :

- **Max-pooling** **:** It selects maximum element from the feature map. The resulting max-pooled layer holds important features of feature map. It is the most common approach as it gives better results.
- **Average pooling :** It involves average calculation for each patch of the feature map.

![img](https://i1.wp.com/developersbreach.com/wp-content/uploads/2020/08/PoolingBanner-edited-1.png?w=760&ssl=1)

#### **Why pooling is important ?**

It progressively reduces the spatial size of representation to reduce amount of parameters and computation in network and also controls overfitting. If no pooling, then the output consists of same resolution as input.

There can be many number of convolution, ReLU and pooling layers. Initial layers of convolution learns generic information and last layers learn more specific/complex features. After the final Convolution Layer, ReLU, Pooling Layer the output feature map(*matrix*) will be converted into vector(*one dimensional array*). This is called **flatten layer**.

------

### **Fully Connected Layer**

Fully connected layer looks like a regular neural network connecting all neurons and forms the last few layers in the network. The output from flatten layer is fed to this fully-connected layer.

The feature vector from fully connected layer is further used to classify images between different categories after training. All the inputs from this layer are connected to every activation unit of the next layer. Since all the parameters are occupied into fully-connected layer, it causes overfitting. **Dropout** is one of the techniques that reduces overfitting.

### **Dropout**

Dropout is an approach used for *regularization* in neural networks. It is a technique where randomly chosen nodes are ignored in network during training phase at each stage.

This dropout rate is usually 0.5 and dropout can be tuned to produce best results and also improves training speed. This method of regularization reduces node-to-node interactions in the network which leads to learning of important features and also helps in generalizing new data better.

------

### **Soft-Max Layer**

Soft-max is an activation layer normally applied to the last layer of network that acts as a classifier. Classification of given input into distinct classes takes place at this layer. The soft max function is used to map the non-normalized output of a network to a probability distribution.

- The output from last layer of fully connected layer is directed to soft max layer, which converts it into probabilities.
- Here soft-max assigns decimal probabilities to each class in a multi-class problem, these probabilities sum equals 1.0.
- This allows the output to be interpreted directly as a probability.

For binary classification problem, logistic function is used and for multi-classification soft-max is used.

------

## Summary

- Input of image data into the convolution neural network, which is processed with help of pixel values of the image in convolution layer.
- Filters are generated that performs convolutions over entire image and trains the network to identify and learn features from image, which are converted to matrices.
- Batch normalization of input vectors is performed at each layer, so as to ensure all input vectors are normalized and hence regularization in network is attained.
- The convolutions are performed until better accuracy has attained and maximum feature extraction is done.
- Convolutions results in sub-sampling of image and dimensions of input gets changed according to padding and stride chosen.
- Each convolution follows activation layer(ReLU) and pooling layer, which brings in non-linearity and helps in sub sampling respectively.
- After the final convolution, the input matrix is converted to feature vector. This feature vector is the flattened layer.
- Feature vector serves as input to next layer(fully connected layer), where all features are collectively transferred into this network. Dropout of random nodes occurs during training to reduce overfitting in this layer.
- Finally, the raw values which are predicted output by network are converted to probabilistic values with use of soft max function.