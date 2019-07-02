---
layout: post
current: post
cover: assets/images/c5.jpg
navigation: True
title: Generative Model 1 - PixelCNN & PixelRNN
date: 2019-07-01
comments: true
---


**I plan to introduce three the-state-of-art generative models: PixelCNN and PixelRNN, Variational Autoencoders(VAEs) and Generative Adversarial Networks(GANs). In this article, I am going to introduce the first one: PixelCNN and PixelRNN**

------------------

In my <a href="" style="font-weight:normal;">previous blog</a>, I differentiated two machine learning model types: **Discriminative model** and **Generative model**. Let's recap before we dive into PixelCNN and PixelRNN. So, discriminative model models the conditional distribution 
$$p(y|x)$$ 
and is interested in taking input x and then outputting label y. On the other hand, generative model models the joint distribution $$p(x, y)$$ and is instead interested in how data is generated. It can be made sense by decomposing $$p(x, y) = p(y)p(x|y)$$, where the prior $$p(y)$$ generates the label y, and then data x is generated based on y, $$p(x|y)$$. 

**This is the tutorial from Ian GoodFellow in 2016 NIPS**

<img src="assets/images/PixelCNN/Pixel-1.jpg" alt="p1" style="width: 90%;">

PixelCNN and PixelRNN fall in the category of explicit density, more specifically, tractable density.