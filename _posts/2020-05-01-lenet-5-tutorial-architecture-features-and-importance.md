---
title: 'LeNet-5 Tutorial: Architecture, Features and Importance'
date: 2020-05-01
tags:
  - Convolution Neural Network
  - CNN
---

 ![LeNet-5 Tutorial: Architecture, Features and Importance title banner](https://www.analyticssteps.com/backend/media/thumbnail/191202/6751136_1619773364_Lenet-5Artboard%201.jpg)

## **What is a Convolutional Neural Network?**

A Convolutional Neural Network (ConvNet/CNN) is a [Deep Learning algorithm](https://www.analyticssteps.com/blogs/top-deep-learning-algorithms) which is supposed to absorb an input image, assign importance (learnable weights and biases) to varied aspects/objects within the image and be able to differentiate among them. 

The pre-processing needed during a ConvNet is far lower as compared to other classification algorithms. whereas in primitive strategies filters are hand-engineered, with enough training, ConvNets have the power to study and learn these filters/characteristics.

The design of a ConvNet is analogous to the property pattern of Neurons in the Human Brain and was galvanized by the organization of the Visual Cortex. Individual neurons reply to stimuli solely during a restricted region of the visual view called the Receptive Field. a group of such fields overlap to hide the whole visual area.

## **History of LeNet-5**

LeNet-5 was one among the earliest [convolutional neural networks](https://www.analyticssteps.com/blogs/how-transfer-learning-done-neural-networks-and-convolutional-neural-networks) which promoted the event of deep learning. After innumerous years of analysis and plenty of compelling iterations, the end result was named LeNet-5 in 1988.

In 1989, Yann LeCun et al. at Bell Labs initially applied the backpropagation rule to all sensible applications, and believed that the flexibility that is provided in network generalization can be increased dramatically by providing constraints from the tasks domain. 

He combined a convolutional neural network trained by backpropagation algorithms to scan written numbers which he later used in detecting different handwritten postcode numbers which were given by the USA communicating Service. This was the elementary example of what later came to be referred to as LeNet. 

Within the same year, LeCun explained a tiny handwritten digit recognition anomaly in another paper, and proved that by even supposing the problem is linearly separable, single-layer networks do tend to exhibit poor generalization skills. 

Once anomaly is removed using invariant feature detectors on a multi-layered, unnatural network, the model may perform extremely well. He believed that these results verified that minimizing the amount of free parameters within the neural network could enhance the generalization of the same.

## **What is LeNet-5?**

In 1989, Yann LeCun presented a convolutional neural network named LeNet. In general, LeNet refers to LeNet-5 and is a straightforward convolutional neural network. 

Convolutional neural networks are a form of feed-forward neural network whose artificial neurons will answer a locality of the encircling cells within the coverage vary and perform well in large-scale image processing.

The LeNet-5 means the emergence of CNN and defines the fundamental elements of CNN. However it absolutely was not in style at that point due to the dearth of hardware equipment, particularly GPU (Graphics process Unit, a specialised electronic circuit designed to alter memory to accelerate the creation of pictures during a buffer meant for output to a show device) and different algorithm, appreciate SVM are able to do similar effects or maybe exceed the LeNet.

### **Features of LeNet-5**

- Every convolutional layer includes three parts: convolution, pooling, and nonlinear activation functions
- Using convolution to extract spatial features (Convolution was called receptive fields originally)
- Subsampling average pooling layer
- tanh activation function
- Using MLP as the last classifier
- Sparse connection between layers to reduce the complexity of computation

(Must check: [7 Types of Activation Functions in Neural Networks](https://www.analyticssteps.com/blogs/7-types-activation-functions-neural-network))

## **Architecture of LeNet-5**

LeNet-5 CNN architecture is made up of 7 layers. The layer composition consists of 3 convolutional layers, 2 subsampling layers and 2 fully connected layers.

------

**![Displaying the architecture of LeNet-5](https://lh4.googleusercontent.com/wQObukjeIYDLKYZML29IB05ggfL5tCWsGlaqCEs206wMfo4y0UPsnvo1aF7WJg30aHxLKpFp3yTAqVpDMuD5GbhMUL2Y91J8gMAEhwzgwyRmrcuH9YOm7hW673toraHjYxxlEP_h)**

*LeNet-5 architecture,[Source](http://vision.stanford.edu/cs598_spring07/papers/Lecun98.pdf)*

The above diagram shows a description of the LeNet-5 architecture as shown in the original document.

- **Layer 1-** The first layer is the input layer; It is generally not considered a layer of the network as nothing is learned on that layer. The input layer supports 32x32, and these are the dimensions of the images that will be passed to the next layer. Those familiar with the MNIST dataset will know that the images in the MNIST dataset are 28 x 28 in dimensions. In order for the dimension of MNIST images to meet the requirements of the input layer, the 28x28.

- **Layer 2-** Layer C1 is a convolution layer with six 5 × 5 convolution kernels, and the feature allocation size is 28 × 28, whereby input image information can be avoided.

- **Layer 3-** Layer S2 is the undersampling / grouping layer which generates 6 function graphs of length 14x14. Each cell in every function map is attached to 2x2 neighborhoods at the corresponding function map in C1. 

- **Layer 4-** C3 convolution layer encompass sixteen 5x5 convolution kernels The input of the primary six function maps C3 is every continuous subset of the 3 function maps in S2, the access of the following six function maps comes from the access of the 4 continuous subsets and the input for the following 3 function maps is crafted from the 4 discontinuous subsets.Finally, the input for the very last function diagram comes from all the S2 function diagrams. 

- **Layer 5-** Layer S4 is just like S2 with a length of 2x2 and an output of sixteen 5x5 function graphics. 

- **Layer 6-** Layer C5 is a convolution layer with one hundred twenty convolution cores of length 5x5. Each cell is attached to the 5x5 neighborhoods along sixteen S4 function charts. Since the function chart length of S4 is likewise 5x5, the output length of C5 is 1 * 1, so S4 and C5 are absolutely linked. It is referred to as a convolutional layer in preference to a completely linked layer due to the fact if the input of LeNet-5 becomes large and its shape stays unchanged, then its output length is bigger than 1x1, i.e. now no longer a completely linked layer.

- **Layer 7-** The F6 layer is connected to C5 and 84 feature charts are generated. In the grayscale images used in the research, the pixel values from 0 to 255 were normalized to values between -0.1 and 1,175 The reason for normalization is to make sure the image stack has a mean of 0 and a standard deviation of 1. The advantages of this are in the reduction of the training time. In the following example we will normalize the pixel values of the images to take values between 0 and 1.

 

The LeNet-5 architecture uses two significant types of layer construction: 

- Convolution layers
- Subsampling layers

The first official convolution layer C1 generates 6 as maps of the output properties and has a kernel size of 5x5. The kernel / filter is the name of the window that contains the weight values that are used during the convolution of the weight values with the input values.

The size of the local receiving field of each unit or each neuron within a convolutional layer.The dimensions of the six feature maps that the first convolution layer produces are 28 x 28.

------

**![the original document showing the layer type, the number of feature maps, number of kernels, input size and the activation function for the different layers.](https://lh6.googleusercontent.com/FsUiQ8z7kGH5bQuwT1xO1DKFAL3JA8-aJ0SV29_8d5_8OWeJlz-OyjSYBYKnUEtPgZ5QdgEliePuUhh5StLErcgCCd5_UMifVoISf9pDiCbibg6ri_Qo-AJ1uKXfHmjzJgsH3X3s)** 

*LeNet CNN Structure*

------

A "S2" subsampling layer follows the "C1" layer. The "S2" level halves the dimension of the feature maps it receives from the previous level. This is commonly known as subsampling.

Layer "S2" also generates 6 feature cards, each corresponding to feature cards that were passed as input from the previous layer. This link contains more information on subsampling slices.

## **Importance of LeNet-5**

LeNet-5 was popularly used on a massive scale to routinely classify hand-written digits on financial institutions such as banks. CNNs are the muse of present day state-of-the artwork deep learning which is primarily based on [computer vision](https://www.analyticssteps.com/blogs/what-computer-vision-and-how-does-it-work). 

These networks are constructed upon three principal ideas: neighborhood receptive fields, shared weights and spatial subsampling. Neighbourhood receptive fields with shared weights are the quintessential soul of the convolutional layer and most architectures defined under use convolutional layers in a single shape or another.

Another cause why LeNet is a critical structure is that earlier than it was invented, character recognition was performed in most cases with the aid of using expertise of feature engineering by hand, accompanied with the aid of using a machine learning model to discover ways to classify hand engineered capabilities. 

LeNet made hand engineering capabilities redundant, due to the fact the community learns the first-rate inner illustration from raw images instantly.

## **Conclusion**

All the features and the build of the network proves that it is a great model for beginners and for all who are looking on how to start understanding the working of CNNs. The functionality of convolutional, pooling and fully connected layers are well explained through this neural network.

Now that you possess this knowledge about the LeNet networks, you can utilize prebuilt LeNet models or even implement one yourself with libraries like PyTorch or [Keras](https://www.analyticssteps.com/blogs/keras-tutorial-neural-network-library-deep-learning). Keep in mind the limitations of LeNet, and you should achieve decent results by training it yourself with the MNIST dataset.



[https://www.analyticssteps.com/blogs/lenet-5-tutorial-architecture-features-and-importance](https://www.analyticssteps.com/blogs/lenet-5-tutorial-architecture-features-and-importance)