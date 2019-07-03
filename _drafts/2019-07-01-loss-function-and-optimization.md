---
layout: post
current: post
cover: assets/images/cover-img/c5.jpg
navigation: True
title: Loss Function and Optimization
date: 2019-07-01
comments: true
---


**In this article, I will be talking about some loss functions and the metric of choosing the right one for your models and I will expound it mainly using examples of supervisd learning**

------------------

So in supervised learning, we are givien data as a pairs of input and label: $$(x,y)$$. The process of learning or optimization is to find the best pair of feature weight matric(W) and bias(b): $$(W,b)$$. So people might be wondering how exactly shall we find the best pair, or what is the signal that tells us, ok this is the best pair and we can just stop here?  

In Machine Learning, we use **Loss Function** as the metric for evaluating the badness of our model, i.e. it tells us how good our current model is.

Given a dataset of example $$\{(x_i, y_i)\}^N_{i=1}$$ where $$x_i$$ is image and $$y_i$$ is an integer label; then the loss over the data set is a sum of loss over examples:

$$ L = \frac{1}{N} \sum_i{L_i(f(x_i, W), y_i)}$$

where $$f(x_i, W)$$ computes the predicted label with current pair of W and b, and $$L_i$$ is the loss function on the $$i^{th}$$ sample in the dataset.

Now we have the function that can actually quantify the loss, the learning or optimizing process is naturally minimizing the loss so as to get the best model, i.e. we want to get the the pair of (W, b) that minimizes the Loss Function L: 

$$\underset{W,b}{\operatorname{arg\min}} \quad L$$

Obviously, there are tons of loss functions and each of them has its own use case depending on what kind of losses we are trying to measure, and the choosing of loss function is a vital step in our learning process. I will introduce some common loss functions and their use cases and give some ideas of how to choose them depending on different problems.

<br><br><br>

**Binary Classification**

Image we have a set of emails that are either spam or non-spam. We want to train a model that when given a new email, it is able to tell us whether it is a spam email or not. Here the input of our model is an email, and the label is spam or non-spam, represented with 1 and 0, respectively. 

<img src="assets/images/LF/LF-1.jpg" alt="LF1" style="width:80%;">

We already know that the purpose of loss functions is to measure how bad our model is. Hence we want to capture the error of spam filtering result. For example, if the input is a non-spam email and our model wronly classifies it as a spam, this should be captured by our loss function. Since we have a special case of 0/1 situation, we are encouraged to use a hard-margin loss function: 


$$ L_i = \left[\!\left[ y_i = f(x_i, W) \right]\!\right] $$

where

$$
\left[\!\left[ y_i = f(x_i, W) \right]\!\right] =
\begin{cases}
1,  & \text{if} \ \ y_i \neq f(x_i, W) \\[2ex]
0,  & \text{if} \ \ y_i = f(x_i, W)
\end{cases}
$$

The goal is to classify all the emails correctly, thereby $$ L = 0 $$.


<br><br><br>

**Multi-class Classification**

Let's generalize the binary case to multiclass case. Suppose we have a three-class classification problem which we want to classify an image to either cat, car, or frog. 

<img src="assets/images/LF/LF-2.jpg" alt="LF2" style="width:70%;">

Unlike binary classification where we label each data either 0 or 1, we attach a score to each classes, and by convention, the class with the highest score would be our predicted label. For example, in above toy task, we train the model with 3 examples. After the first epoch, we end up with 5.1 of car for a cat image while the score for cat is 3.2. That means we got the wrong classification with a relativly small error. However, we correctly classify the car image with the highest 4.9 score attached to car. Unfortunately, we totally screw up with frog classification by attaching the least score to frog, which is very bad. 

So in order to capture this type of error, which loss function are we supposed to use? It turns out that what's called multi-class SVM loss function would be a good candidate.

$$
\begin{align}
L_i &= \sum_{j \neq y_i}
\begin{cases}
0,  & \text{if} \ \ s_{y_i} \ge s_j + 1 \\[2ex]
s_j - s_{y_i} + 1,  & \text{otherwise}  
\end{cases}\\
&= \sum_{j \neq y_i} \text{max}(0, s_j - s_{y_i} + 1)
\end{align}
$$

Where the constant 1 is just a safe threshold, and $$s$$ is a shorthand for the scores vector: $$s = f(x_i, W)$$.

This loss function is also called "Hinge Loss" 

<img src="assets/images/LF/LF-3.jpg" alt="LF3" style="width:60%;">

Let's make sense of this hinge loss in detail. 

$$L_i = \sum_{j \neq y_i} \text{max}(0, s_j - s_{y_i} + 1)$$ means nothing but the sum of score error of each pair of classes, after looping through every pair except for correct class itself.

For example, for the first image, cat is the correct class, so we look at (cat, car) and (cat, frog) and express their error by  $$ \text{max}(0, s_j - s_{y_i} + 1) $$, which is 

$$
\begin{align}
L_1 &= \text{max}(0, 5.1-3.2+1) + \text{max}(0, -1.7-3.2+1) \\
&= \text{max}(0, 2.9) + \text{max}(0, -3.9) \\
&= 2.9 + 0 \\
&= 2.9
\end{align}
$$

Similarly,

$$
\begin{align}
L_2 &= \text{max}(0, 1.3-4.9+1) + \text{max}(0, 2.0-4.9+1) \\
&= \text{max}(0, -2.6) + \text{max}(0, -1.9) \\
&= 0 + 0 \\
&= 0
\end{align}
$$

$$
\begin{align}
\qquad \ L_3 &= \text{max}(0, 2.2-(3.1)+1) + \text{max}(0, 2.5-(-3.1)+1) \\
&= \text{max}(0, 6.3) + \text{max}(0, 6.6) \\
&= 6.3 + 6.6 \\
&= 12.9
\end{align}
$$

<img src="assets/images/LF/LF-4.jpg" alt="LF4" style="width:60%;">

and the final total loss $$ L = (2.9+0+12.9)/3 = 5.27$$, which is a pretty large loss.



**Note**: 
1. We choose 1 as the safe shreshold here but the value actually does not matter, because we can scale up or down the feature weight $$W$$ to offset any scale on this saft shredhold constant.

2. If the score changes a little bit, say the car score changes from 4.9 to 4.5, the loss reamins the same. Yet it could be sensitive.

3. The min possible loss is 0 when every class is labeled correctly and the max possible loss could be infinite when the miss labled score is infinitely higher than the score for correct label.

4. We skip looping with $$j = y_i$$ because we want our min loss to be 0, which makes more sense. However, looping over all classese including $$j = y_i$$ won't change our training result but just that the min loss will be 1.

5. We can do a sanity test before go further with optimization. At initialization all s will be approximately equal, which gives us the loss of $$C-1$$. e.g. 10-1 = 9. So we can check if we indeed get this value initailly. If not, there must be some bugs in the program and we should fix them before going further.


So let's think about this question: why don't we use $$L_i = \sum_{j \neq y_i} \text{max}(0, s_j - s_{y_i} + 1)^2$$ or some other seemingly reasonable functions?




It turns out that in practical, there is another loss function that is more common that Hinge loss and that is Cross-entrophy loss.

In Hinge loss, we just say that we want the correct class to have the highest score but we do not give any meanings for these scores attached to each class. However, in Cross-entrophy loss, we interpret raw classifier scores as probabilities using softmax function.

$$P(Y=k|X=x_i) = \frac{e^{s_k}}{\sum_je^{s_j}}$$

where, again $$s = f(x_i; W)$$ is the shorthand for scores.


Thus, we define the loss function: 

$$ 
\begin{align}
L_i &= -logP(Y=y_i|X=x_i)  \\
&= -log \frac{e^{s_{y_i}}}{\sum_je^{s_j}}
\end{align}
$$


**There are two views to interpret this loss:**


**Information theory.** In information theory, the cross-entrophy between a true distribution p and an estimated distribution q is defined as

$$H(p,q) = - \sum_xp(x)\text{log}q(x)$$

The Softmax classifier is hence minimizing the cross-entrophy between the estimated class probabilities ($$q = \frac{e^{s_{y_i}}}{\sum_je^{s_j}}$$ as seen above) and the true distribution, which in this interpretation is the distribution where all probability mass is on the correct class(i.e. p=\[0,...,1,...0\] contains a single 1 at the $$y_i^{th} position$$). Moreover, since the cross-entropy can be written in terms of entropy and the KL divergence as $$H(p,q) = H(p) + D_{KL}(p\|q)$$, and the entropy of p is 0, this is also equivalent to minimizing the KL divergence between the two distributions. In other words, the cross-entropy objective wants the predicted distribution to have all of its mass on the correct answer.

**Probablistic view.** Looking at the expression, we see that

$$P(y_i|x_i; W) = \frac{e^{s_{y_i}}}{\sum_j{e^{s_j}}}$$

can be interpreted as the normalized probability assigned to the correct label $$y_i$$ given the image $$x_i$$ and parameterized by $$W$$. To see this, remember that the Softmax classifier interprets the scores inside the output vector $$s$$ as the unnormalized log probabilities. Raising it to exponential therefore gives the probabilities and the division perfoems the normalization so that the probabilities sum to 1. In the probabilistic interpretation, we are therefore minimizing the negative log likelihood of the correct class, which can be interpreted as performing Maximum Likelihood Estimation (MLE).


If we look at previous toy example, the loss of, say cat classification is 

$$ L_1 = -log(0.13) = 0.89 $$

<img src="assets/images/LF/LF-5.jpg" alt="LF5" style="width:100%;">



**Note**

1. The theoretical min/max possible loss $$L_i$$ is 0 and infinity repectively. We say it's theoretical because we can get 0 only when correct class score is plus infinity while all other scores are negative inginity, which is rare in practice. Same thing happens to the infinity case. 

2. Like sanity test for SVM loss, at initialization all s will be approximately equal, which gives us a loss of $$log(C)$$. e.g. $$log(10) \approx 2.3$$.

3. Unlike SVM loss that jiggling a bit (changing score slightly) will not make difference to the loss, the loss will be changed in the case of Softmax loss becaue in Softmax, We always intend to improve the probability of correct class towards 1.