---
layout: post
current: post
cover: assets/images/cover-img/c5.jpg
navigation: True
title: Generative Model 2 - Variational AutoEncoder(VAE)
date: 2019-06-29
comments: true
---


**In this article, I will talk about Variational AutoEncoder and its use case**

------------------

Before I talk about Variational AutoEncoder(VAE), let's first take a look at AutoEncoder in general.

```AutoEncoder``` is an unsupervised approach for training a **lower-dimensional** feature representation from unlabeled training data. The aim of AutoEncoder is not to generate data(which is exactly what VAE does), but to reconstruct data.


So in this case, we have some input data x, and we want to learn some features called z. And we will have an encoder which maps input x to features z. This encoder can take many forms but generally we use Neural Networks from sigmoid in early times to deeper fully-connected nets to ReLU CNN lately.
<img src="assets/images/VAE/VAE-1.jpg" alt="VAE-1" style="width: 50%;">

Note: We usually have smaller z than x(dimentionality reduction), because we want to use z to capture the most important and meaningful features that can describe the input x.

Now the question is once we have features z, how do we learn from this features?

What AutoEncoder does is to train the model such that the features can be used to reconstruct original data x. We use another Neural Nets(same type of encoder) as a decoder to take features z and output a result that has the same dimention as original input x and have it be similar to x. This is a process of reconstruction.

<img src="assets/images/VAE/VAE-2.jpg" alt="VAE-2" style="width: 40%; float: left;">
<img src="assets/images/VAE/VAE-3.jpg" alt="VAE-3" style="width: 20%;">

In order to train this model, we will use L2 loss function. Basically, we this says is let's make the pixels of output $$\hat x$$ be the same as the pixels of original input x as much as possible.

<img src="assets/images/VAE/VAE-4.jpg" alt="VAE-4" style="width: 50%;">

Note: Although here we use the loss function, we do not have an external label. All we need is the training data that is used both to be passed to our training functions as well as to compute the loss.


Remember that AutoEncoder is defined as a technique for dimension reduction. So **after training** this model, we can throw away the decoder and use fetures z as the input for supervised learning, for example, classification.

<img src="assets/images/VAE/VAE-5.jpg" alt="VAE-5" style="width: 50%;">


So intuitively, fetures z that we learn in AutoEncoder has the capability of being able to capture some meaningful and important factors of variations of original data. Now based off this intuition, a natural question is can we use these latent features z and use the similar setup to generate new data, in the above example, new images.This is when AVE comes in.

So we assume that training data $$\{x^{(i)}\}^N_{i=1}$$ is generated from underlying unobserved(latent) representation z. So we want to use a prior distribution $$P_{\theta^*}(z)$$ to represent how these features z might be generated. And a typical prior would be Guassian. Then we are going to generate the data x by sampling from a conditional distribution given z. 
$$p_{\theta^*}(x|z^{(i)})$$

<img src="assets/images/VAE/VAE-6.jpg" alt="VAE-6" style="width: 50%;">

Intuition: x is an image, z is latent factors used to generate x: attributes, orientation, color, etc.


Goal: we want to estimate the true parameters $$\theta^*$$ of this generative model.


**How should we represent this model?**

- Choose prior P(x) to be simple, e.g. Gussian
- Conditional P(x|z) is complex(generates image) $$\to$$ represent with Neural Networks, and we are going to call it decoder network
<img src="assets/images/VAE/VAE-7.jpg" alt="VAE-7" style="width: 50%;">


**How to train this model?**

Remember a strategy for trainig generative models: learn model parameters to maximize likelihood of training data 

$$P_{\theta} = \int P_{\theta}(z) P_{\theta}(x|z)dz$$

However, we have a problem with this method: This integral is not tractable. 
<img src="assets/images/VAE/VAE-8.jpg" alt="VAE-8" style="width: 50%;">
we are good with $$P_{\theta}(z)$$(Guassian) and $$P_{\theta}(x|z)$$(encoder network) but the integral is not tractable if we want to compute  $$P_{\theta}(x|z)$$ for every z.

So data likelihood is intractable. In fact, if we look at other alternative, e.g. posterior density, it is also intractable.
<img src="assets/images/VAE/VAE-9.jpg" alt="VAE-9" style="width: 60%;">


So we can't directly optimize this. 

**Solution**

a solution to this problem is, in addition to decoder network modeling 
$$p_{\theta}(x|z)$$, we define additional encoder network $$q_{\phi}(z|x)$$ that approximates $$p_{\theta}(z|x)$$, which is also intractable.

We will see that this allows us to deriive a lower bound on the data likelihood that is tractable, which we can optimize.

<img src="assets/images/VAE/VAE-10.jpg" alt="VAE-10" style="width: 80%;">

Once you have the mean and covariance matrix, you can sample data from the distribution.

Note: Encoder and decoer networks also called "recognition/inference" and "generation" netoworks

Now, equipped with our encoder and decoder networks, let's work out the log data likelihood:

$$
\begin{align}
\qquad \qquad \qquad \qquad log \ p_{\theta}(x^{(i)}) &= E_{z \sim q_{\phi}(z|x^{(i)})} \left[ log \ p_{\theta}(x^{(i)}) \right] \qquad (p_{\theta}(x^{(i)}) \ does \ not \ depend \ on \ z)\\n
&= E_z \left[ log \ \frac{p_{\theta}(z) p_{\theta}(x^{(i)}|z)}{p_{\theta}(z|x)} \right] \qquad (Bayes' \ Rule)\\
&= E_z \left[ log \ \frac{p_{\theta}(z) p_{\theta}(x^{(i)}|z)}{p_{\theta}(z|x)} \frac{q_{\phi}(z|x^{(i)})}{q_{\phi}(z|x^{(i)})}\right] \qquad (Multiply \ by \ 1)\\
&= E_z \left[ log \ p_{\theta}(x^{(i)}|z) \right] - E_z \left[ log \ \frac{q_{\phi}(z|x^{(i)})}{p_{\theta}(z)} \right] + E_z \left[ log \ \frac{q_{\phi}(z|x^{(i)})}{p_{\theta}(z|x^{(i)})} \right] \\ 
&= E_z \left[ log \ p_{\theta}(x^{(i)}|z) \right] -D_{KL}(q_{\phi}(z|x^{(i)})\|p_{\theta}(z)) + D_{KL}(q_{\phi}(z|x^{(i)})\|p_{\theta}(z|x^{(i)}))\\


& \qquad \qquad \uparrow \qquad \qquad \qquad \qquad \qquad \qquad \uparrow \qquad \qquad \qquad \qquad \qquad \qquad \uparrow \\

& \color{red}{Decoder \ network \ gives \qquad \quad \ This \ KL \ term(btw \qquad \qquad p_{\theta}(z|x) \ intractable \ so \ we \ can't} \\
& \color{red}{p_{\theta}(x|z), \ can\ compute \qquad \qquad Guassians \ for \ encoder \qquad compute \ this \ KL \ term, \ but}\\
& \color{red}{estimate \ of \ this \ term \qquad \quad \ \ and \ z \ prior \ has \ closed \qquad \ \ we \ know \ that \ KL \ divergence} \\
& \color{red}{through \ sampling \qquad \qquad \quad \ form \ solution \qquad \qquad \qquad always \ge 0}
\end{align}
$$

So, we now have a tractable lower bound which we can optimize

<img src="assets/images/VAE/VAE-11.jpg" alt="VAE-11" style="width: 100%;">

and then 

<img src="assets/images/VAE/VAE-12.jpg" alt="VAE-12" style="width: 100%;">


Hence, now the task is to maximize the lower bound

$$E_z \left[ log \ p_{\theta}(x^{(i)}|z) \right] -D_{KL}(q_{\phi}(z|x^{(i)})\|p_{\theta}(z))$$

<img src="assets/images/VAE/VAE-13.jpg" alt="VAE-13" style="width: 100%;">

To generate data, we just use decoder network 
<img src="assets/images/VAE/VAE-14.jpg" alt="VAE-14" style="width: 80%;">

Note: Different dimensions of latent variable z encode intepretable factors of variation 


**Summary**

<img src="assets/images/VAE/VAE-15.jpg" alt="VAE-15" style="width: 100%;">

