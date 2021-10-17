---
title: 'Convolutional'
date: 2021-06-04
tags:
  - Convolutional
---



Convolution Neural Network is one among various types of Deep Learning Neural Networks. CNN is very powerful and widely used in image classification, image recognition, computer vision etc.

# Convolution



In [mathematics](https://en.wikipedia.org/wiki/Mathematics) (in particular, [functional analysis](https://en.wikipedia.org/wiki/Functional_analysis)), **convolution** is a [mathematical operation](https://en.wikipedia.org/wiki/Operation_(mathematics)) on two [functions](https://en.wikipedia.org/wiki/Function_(mathematics)) (*f* and *g*) that produces a third function f*g that expresses how the shape of one is modified by the other. The term *convolution* refers to both the result function and to the process of computing it. It is defined as the [integral](https://en.wikipedia.org/wiki/Integral) of the product of the two functions after one is reversed and shifted. The integral is evaluated for all values of shift, producing the convolution function.

## Definition

The convolution of *f* and *g* is written *f*∗*g*, denoting the operator with the symbol ∗.[[B\]](https://en.wikipedia.org/wiki/Convolution#cite_note-3) It is defined as the integral of the product of the two functions after one is reversed and shifted.

![image-20211016131400122](../images/image-20211016131400122.png)

## Lessons from a Dropped Ball

Imagine we drop a ball from some height onto the ground, where it only has one dimension of motion. *How likely is it that a ball will go a distance cc if you drop it and then drop it again from above the point at which it landed?*

Let’s break this down. After the first drop, it will land aa units away from the starting point with probability f(a)f(a), where ff is the probability distribution.

Now after this first drop, we pick the ball up and drop it from another height above the point where it first landed. The probability of the ball rolling bb units away from the new starting point is g(b)g(b), where gg may be a different probability distribution if it’s dropped from a different height.

![img](../images/ProbConv-fagb.png)

If we fix the result of the first drop so we know the ball went distance aa, for the ball to go a total distance cc, the distance traveled in the second drop is also fixed at bb, where a+b=ca+b=c. So the probability of this happening is simply f(a)⋅g(b)f(a)⋅g(b).[1](https://colah.github.io/posts/2014-07-Understanding-Convolutions/#fn1)

Let’s think about this with a specific discrete example. We want the total distance cc to be 3. If the first time it rolls, a=2a=2, the second time it must roll b=1b=1 in order to reach our total distance a+b=3a+b=3. The probability of this is f(2)⋅g(1)f(2)⋅g(1).
