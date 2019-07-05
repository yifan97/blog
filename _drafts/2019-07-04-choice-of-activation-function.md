---
layout: post
current: post
cover: assets/images/cover-img/c9.jpg
navigation: True
title: How to Choose Activation Functions
date: 2019-06-28
comments: true
---


**In this article, I will talk about some common activation functions in neural nets and how to choose appropriate ones**

------------------

There are many activation functions that are used in neural net

<img src="assets/images/AF/AF-1.jpg" alt="AF-1" style="width: 80%;">


I will mainly talk about three most commonly used ones and analyze their pros and cons.


**Sigmoid**

$$\sigma(x) = \frac{1}{1+e^{-x}}$$

<img src="assets/images/AF/AF-2.jpg" alt="AF-2" style="width: 40%;">

- squashes the input to the range [0,1]

- saturated neurons 'kill' the gradients

<img src="assets/images/AF/AF-3.jpg" alt="AF-3" style="width: 80%;">

if we look at the gradient flows, what is $$\frac{\partial\sigma}{\partial{x}}$$ when x takes different values? 

- When x = -10,  $$ \frac{\partial\sigma}{\partial{x}} \approx 0 $$ 
- When x = 0,  $$ \frac{\partial\sigma}{\partial{x}}$$ performs normally 
- When x = 10,  $$ \frac{\partial\sigma}{\partial{x}} \approx 0 $$

As we can see, when x is very large or very small, it will kill off the gradient.

- sigmoid outputs are not zero-centered 

Let's look at this neuron where the sum is passed to the neuron and is calculated by a activation function, and then the output of it will be then passed to anpther layer. 

<img src="assets/images/AF/AF-4.jpg" alt="AF-4" style="width: 80%;">

Considering the case when the input of to a the neuron is awlways positive,i.e. **x** > **0**. In this case, what can we say abou the gradients on **w**? 

The answer is that it will always all postive or all negative, depending on whether the upstream gradient is positive or negative. And this will slow our optimization process largely. Let's illustrate how by assuming two dimemtional **w**: 

<img src="assets/images/AF/AF-5.jpg" alt="AF-5" style="width: 50%;">

only all positive and all negative **w** being allowed means we can only update in certain directions, precisely upper right or bottom left whereas the blue stright arrow represents the fast and ideal direction to update towards lowest point. We can easily tell from the graph that because of direction limitaion, we have to go in a zigzag path, which will slow donw the updating speed. In practical when we have drastically more parameters, this problem can be serious. 

- Exponential computation is expensive

In sigmoid, we have $$e^{(...)}$$ which is relatively computationally expensive. However, this is just a minor problem.

<br>
**tanh**

$$tanh(x) =  \frac{sinh(X)}{cosh(X)}$$

<img src="assets/images/AF/AF-6.jpg" alt="AF-6" style="width: 40%;">

- Squashes numbers to range [-1, 1]

- It is zero-centerd

- However, it still kills gradients when saturated.

<br>
**ReLU**

$$ReLU(x) = max(0, x)$$

<img src="assets/images/AF/AF-7.jpg" alt="AF-7" style="width: 40%;">

- Does not saturate in + region

- Very computationally efficient

- Converges much faster than sigmoid/tanh in practive(e.g. 6x)

- However, it is back to not zero-centered 

- In - region we do have saturation 

<br>
**Learky ReLU**

$$Leaky ReLU(x) = max(0.01x, x)$$

<img src="assets/images/AF/AF-8.jpg" alt="AF-8" style="width: 40%;">

- Does not saturate in all regions

- Very computationally efficient

- Still converges much faster than sigmoid/tanh in practive(e.g. 6x)

<br>
**ELU**

$$ELU(x)  =
\begin{cases}
x,  & \text{if $x$ > 0} \\
\alpha(e^x-1), & \text{if $x \le$ 0}
\end{cases}
$$

<img src="assets/images/AF/AF-9.jpg" alt="AF-9" style="width: 40%;">

- It has all benefits of ReLU

- It is closer to zero mean outputs

- However, now it needs compute $$e^{(...)}$$


The general rule of thumb in practice would be:

- Use **ReLU** but be careful with your learning rates

- Try out **Leadky ReLU/ Maxout/ ELU**

- Try out **tanh** but don't expect much

- <span style="color:red">Don't use Sigmoid</span>