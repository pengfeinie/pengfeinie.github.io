---
title: 'L2 and L1 Regularization in Machine Learning'
date: 2020-05-04
tags:
  - Regularization
---

**Overfitting** is a phenomenon that occurs when a Machine Learning model is constraint to training set and not able to perform well on unseen data. 

![img](../images/overfitting_21.png)

Regularization is a technique used to reduce the errors by fitting the function appropriately on the given training set and avoid overfitting. Regularization is the most used technique to penalize complex models in machine learning, it is deployed for reducing overfitting (or, contracting generalization errors) by putting network weights small. 

### **Mathematical Formula for L1 regularization**



## L2 regularization

In L2 regularization, we shrink the weights by computing the Euclidean norm of the weight coefficients (the weight vector ww); λλ is the regularization parameter to be optimized.

![image-20211021181853979](../images/image-20211021181853979.png)

For example, we can regularize the sum of squared errors cost function (SSE) as follows:

![image-20211021181915149](../images/image-20211021181915149.png)

Intuitively, we can think of regression as an additional penalty term or constraint as shown in the figure below. Without regularization, our objective is to find the global cost minimum. By adding a regularization penalty, our objective becomes to minimize the cost function under the constraint that we have to stay within our "budget" (the gray-shaded ball).

![img](../images/l2.png)

In addition, we can control the regularization strength via the regularization parameter λλ. The larger the value of λλ, the stronger the regularization of the model. The weight coefficients approach 0 when λλ goes towards infinity.

## L1 regularization

In L1 regularization, we shrink the weights using the absolute values of the weight coefficients (the weight vector ww); λλ is the regularization parameter to be optimized.

![image-20211021182121602](../images/image-20211021182121602.png)

For example, we can regularize the sum of squared errors cost function (SSE) as follows:

![image-20211021182151108](../images/image-20211021182151108.png)

At its core, L1-regularization is very similar to L2 regularization. However, instead of a quadratic penalty term as in L2, we penalize the model by the absolute weight coefficients. As we can see in the figure below, our "budget" has "sharp edges," which is the geometric interpretation of why the L1 model induces sparsity.

![img](../images/l1.png)



**links**

1. [https://www.geeksforgeeks.org/regularization-in-machine-learning/](https://www.geeksforgeeks.org/regularization-in-machine-learning/)

2. [http://rasbt.github.io/mlxtend/user_guide/general_concepts/regularization-linear/](http://rasbt.github.io/mlxtend/user_guide/general_concepts/regularization-linear/)

   

