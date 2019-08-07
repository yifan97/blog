---
layout: post
current: post
cover: assets/images/cover-img/c12.jpg
navigation: True
title: Training, Validation and Test Set
date: 2019-07-12
comments: true
---


**In this post, I will explain what is training set, validation set and test set. I will also differentiate them in terms of machine learning process**

------------------

Machine learning is all about training exsited data to fit the best model so that this model can be used for future data. To some extend, training data is the most important process. However, if we use all the existed data to train our model, then we don't have an unbiased way to tell whether our model is good enough for unseen data (It might be perfect for existed data because the model learns some inherent or specific rules from training data and thus ignore the general information. This is called **overfitting**). Hence, it is always important to hold out a portion of existed data as an unbiased way of telling the goodness of a model, and we call this small portion of dataset *Validation set*. It is sometimes used interchangeably with *Test set*, but that is not the best practice. I will explain this later.  


**Training set**

> The sample of data used to fit the model.

Training set, by its name, is used for learning, i.e. to find the best combination of parameters that best fit the data. In perceptron, for example, is to find the optimal weights. 

**Validation set**

> The sample of data used to provide an unbiased evaluation of a model fit on the training dataset while tuning model hyperparameters. The evaluation becomes more biased as skill on the validation dataset is incorporated into the model configuration.

Validation set is used to choose the best model. In training process, we usually have several models trained seperatedly. Validation set is to choose the best one. 

**Test set**

> The sample of data used to provide an unbiased evaluation of a final model fit on the training dataset

Test set is used only to assess the performance of a fully-trained classifier, for example, to estimate the error rate after we have chosen the final model.


<br>
At beginning of this article, I said that Validation set should not be confused with test set. Here's why:

The error rate estimate of the final model on validation data will be biased (usually smaller than the true error rate) since the validation set is used to select the final model. It's very important to note that after assessing the final model on the test set, YOU MUST NOT tune the model any further!

<br>
There is a general thumb of rule for you to tell whether your model is overfitting or not:

- Underfitting – Validation and training error high

- Overfitting – Validation error is high, training error low

- Good fit – Validation error low, slightly higher than the training error