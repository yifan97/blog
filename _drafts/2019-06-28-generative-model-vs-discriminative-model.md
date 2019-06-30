---
layout: post
current: post
cover: assets/images/c5.jpg
navigation: True
title: Generative Model v.s Discriminative Model
date: 2019-06-28
comments: true
---


**In this article, I will talk about what are generative model and discriminative model and what is the difference between them**

------------------

In machine learning, we have two models: generative model and discriminative model. 

Let's explain these two models in the context of classification

Before I get into the topic, I want to tell a story:

A father has two sons, son A and son B. Son A has the ability to understand things in-depth while son B can differentiate things. One day the father brought his two sons to the zoo with just two animals,say Lion and Tiger. After they got out of the zoo, the father gave his two sons a picture of Lion and asked them what animal it is. Son A drew a Lion and a Tiger on the paper based on his image of what he saw in the zoon of these two animals. Then, he compared the picture with two drawings and concluded it was a Lion. Son B, due to his talent for differentiation, told his father immediately it was a Lion. 


We essentially have already experienced these two models. Son B acted like a dicriminative model, which takes features as input and outputs the right classification. Yet, Son A acted like a generative model, which cares more about how the output is generative(as he drew two animals). Theoretically, discriminative models find the conditional probability P(y\|x) whereas generative models find joint probability P(x,y), i.e. P(x\|y) and P(y). After modeling P(y)(called the **class prior**) and P(x\|y), we can then use Bayes rule to derive the posterior distribution on y given x. 


$$P(y|x) = \frac{P(x|y)P(y)}{P(x)}$$

In fact, if were calculating p(y\|x) in order to make a prediction, then we don’t actually need to calculate the denominator, since


$$
\begin{align}
\underset{y}{\operatorname{arg\max}} \quad P(y|x) &= \underset{y}{\operatorname{arg\max}} \quad \frac{P(x|y)P(y)}{P(x)} \\
&= \underset{y}{\operatorname{arg\max}} \quad P(x|y)P(y)
\end{align}
$$


According to Andrew Ng, discriminative model is generally better than generative model in terms of classfication. However, generative model has its own pros. For example, since it models the joint distribution, once you have P(x,y), you can generate likly(x, y) pairs and this can be used for image reconstruction. 



<a href="http://cs229.stanford.edu/notes/cs229-notes2.pdf" style="font-weight:normal;">Andrew Ng</a>

