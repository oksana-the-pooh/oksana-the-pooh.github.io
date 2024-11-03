---
layout: post
title:  "Squared Error in Linear Regression"
date:   2024-10-27 18:19:18 +0300
categories: jekyll update
use_math: true
---
Why do we optimize squared error while train linear regression model?

This is self-treatment post -- I try to overcome my own knowledge gap. That's why I am going to deal with this question in details. Without any "It is easy to see" what is common for popular books and courses. I very well know where this is going. The day will pass and night will  come while you try to see this "easy".

Squared error comes by two ways.

1. The first one is let's use it as common choice and look what it results in.

Let $$x$$ is realization of explanatory variable $$X$$, $$y(x)$$ is prediction (realization of random variable $$Y$$), $$t$$ is target value. The expected value of loss function $$L$$.

$$
E(L)=\iint L(t,y(x))P(x,t)\;dxdt
$$

Let $$L(t,y(x))=(t-y(x))^2$$, then

$$
E(L)=\iint(t-y(x))^2P(x,t)\;dxdt
$$

$$
\frac{\partial E(L)}{\partial y}=2\int (y(x)-t)P(x,t)\frac{1}{|y\prime (x)|}dt=0
$$

$$
\int y(x)P(x,t)\;dt-\int tP(x,t)\;dt=0
$$

$$
\int y(x)P(x|t)P(x)\;dt-\int tP(t|x)P(x)\;dt=0
$$

$$
y(x)-\int tP(t|x)\;dt=0
$$

$$
y(x)=E(t|x)
$$

So when we use squared error as loss function the prediction is expected value of target given explanatory value. It is sensible.

2. About the second way they often say "we use squared error due to likelihood maximization principle".

Let's look to the model

$$
t=Y(X,w)+\xi
$$,

where $$t$$ and $$X$$ are target and explanatory values as previous. In general $$X$$ is a vector. $$w$$ is vector of explanatory variables weights and finally $$\xi$$ is gaussian noise with zero mean.

Why gaussian?

Firstly, if the noise is a sum of independent noises of different nature then by Central Limit Therorem this sum noise is gaussian.

Secondary, it is known from Information Theory that gaussian noise is the worst additive noise because it minimizes channel capacity (maximizes entropy of continuous value). All proofs must hold under the most hard conditions, i.e. for the worst case. Gaussian noise is the case.

Let channel is given by a distribution $$P(y \vert x)$$, then channel capacity

$$C=\max_{P(x): E(x^2)\leq R} I(X;Y),\;\;X\sim N(0,R)$$, where $$R$$ is energy or power constraint of the channel.

$$
C\geq h(X)-h(X|Y)=\frac{1}{2}\log2\pi eR-\frac{1}{2}\log 2\pi e \frac{R\sigma^2}{Re\sigma^2}=\frac{1}{2}\log2\pi e \left(1+\frac{R}{\sigma^2}\right)=C_{gaussian}
$$

Why $$h(X \vert Y)\leq \frac{1}{2}\log2\pi e \frac{R\sigma^2}{Re\sigma^2}$$ ?

$$
Y=X+Z,\;\;E(Z)=0,\; E(Z^2)=\sigma^2
$$

$$
h(X|Y)=h(X|X+Z)=\iint P(x,x+z)\log\frac{1}{P(x|x+z)}dxdz
$$

$$
P(X,X+Z)=P(X|X+Z)P(X+Z)=P(X+Z|X)P(X)
$$

$$P(X+Z \vert X)=P(Z \vert X)=P(Z)$$, since $$X$$ and $$Z$$ are independent.

$$
P(X,X+Z)=P(X)P(Z)
$$

$$
P(X|X+Z)=\frac{P(X)P(Z)}{P(X+Z)}
$$

$$
\log\frac{1}{P(X|X+Z)}=\log\frac{P(X+Z)}{P(X)P(Z)}=\log\frac{\frac{1}{\sqrt{2\pi}\sqrt{\sigma_X^2+\sigma_Y^2}}e^{-\frac{(X+Z)^2}{2(\sigma_X^2+\sigma_Y^2)}}}{\frac{1}{\sqrt{2\pi}\sigma_X}e^{-\frac{X^2}{2\sigma_X^2}}\frac{1}{\sqrt{2\pi}\sigma_Z}e^{-\frac{Z^2}{2\sigma_Z^2}}}=\log\frac{2\pi \sigma_X \sigma_Z}{\sqrt{2\pi}\sqrt{\sigma_X^2+\sigma_Z^2}}e^{-\frac{(X+Z)^2}{2(\sigma_X^2+\sigma_Z^2)}+\frac{X^2}{2\sigma_X^2}+\frac{Z^2}{2\sigma_Z^2}}=\log\frac{2\pi \sigma_X \sigma_Z}{\sqrt{2\pi}\sqrt{\sigma_X^2+\sigma_Z^2}}+\frac{1}{2}\left(\frac{Z^2}{\sigma_Z^2}+\frac{X^2}{\sigma_X^2}-\frac{(X+Z)^2}{\sigma_X^2+\sigma_Z^2}\right)\log e=\log 2\frac{2\pi \sigma_x \sigma_Z}{\sqrt{2\pi}\sqrt{\sigma_X^2+\sigma_Z^2}}+\frac{1}{2}\frac{Z^2\sigma_X^2}{\sigma_Z^2(\sigma_X^2+\sigma_Z^2)}\log e++\frac{1}{2}\frac{X^2\sigma_Z^2}{\sigma_X^2(\sigma_X^2+\sigma_Z^2)}\log e-\frac{XZ}{\sigma_X^2+\sigma_Z^2}\log e
$$

$$
h(X|X+Z)=\iint P(x)P(z)\log\frac{2\pi \sigma_X \sigma_Z}{\sqrt{2\pi}\sqrt{\sigma_X^2+\sigma_Z^2}}dxdz++\iint P(x)P(z)\frac{1}{2}z^2\frac{\sigma_X^2}{\sigma_Z^2(\sigma_X^2+\sigma_Z^2)}\log e\;dxdz+\iint P(x)P(z)\frac{1}{2}x^2\frac{\sigma_Z^2}{\sigma_X^2(\sigma_X^2+\sigma_Z^2)}\log e\;dxdz--\iint P(x)P(z)\frac{xz}{\sigma_X^2+\sigma_Z^2}\log e\;dxdz=\log\frac{2\pi \sigma_X \sigma_Z}{\sqrt{2\pi}\sqrt{\sigma_X^2+\sigma_Z^2}}++\frac{1}{2}\frac{\sigma_X^2}{\sigma_X^2+\sigma_Z^2}\log e+\frac{1}{2}\frac{\sigma_Z^2}{\sigma_X^2+\sigma_Z^2}\log e-\int P(x)\frac{xlog e}{\sigma_X^2+\sigma_Z^2}\underset{=0}{\left(\int P(z)z\;dz\right)}\;dx=\log\sqrt{\frac{2\pi \sigma_X^2 \sigma_Z^2}{\sigma_X^2+\sigma_Z^2}}+\frac{1}{2}\log e=\frac{1}{2}\log 2\pi e \frac{\sigma_X^2\sigma_Z^2}{\sigma_X^2+\sigma_Z^2}
$$

$$
\sigma_X^2\leq R \Rightarrow \frac{\sigma_X^2\sigma_Z^2}{\sigma_X^2+\sigma_Z^2}\leq \frac{R\sigma_Z^2}{R+\sigma_Z^2}
$$

It is easy to check this inequality holds. Indeed,

$$
\sigma_X^2\sigma_Z^2(R+\sigma_Z^2)\leq R\sigma_Z^2(\sigma_X^2+\sigma_Z^2)
$$

$$
\sigma_X^2 R +\sigma_Z^2 \sigma_Z^2 \leq R\sigma_X^2 + R\sigma_Z^2 \Rightarrow \sigma_X^2 \leq R
$$.

And finally

$$
\frac{1}{2}\log 2\pi e \frac{\sigma_X^2 \sigma_Z^2}{\sigma_X^2+\sigma_Z^2} \leq \frac{1}{2}\log 2\pi e \frac{R\sigma_Z^2}{R+\sigma_Z^2} \;\;\Rightarrow\\ \Rightarrow h(X|X+Z)\leq \frac{1}{2}\log 2\pi e \frac{R\sigma^2}{R+\sigma^2}
$$

Then

$$
P(t|X,w,\sigma)=N(t|y(X,w),\sigma)
$$

Let $$t=\begin{pmatrix}t_1 \\ \vdots \\ t_n \\ \end{pmatrix}$$,  $$X=\begin{pmatrix}x_1 \\ \vdots \\ x_n \\ \end{pmatrix}$$, $$y(x,w)=w^\top \phi(x)$$

$$P(t|X,w,\sigma)=\sqcap_{i=1}^{n}N(t_i|w^{\top}\phi(x_i),\sigma)$$

$$
\log P(t|X,w,\sigma)=\sum_{i=1}^{n}\log N(t_i|w^{\top}\phi(x_i),\sigma)=\sum_{i=1}^{n}\log \frac{1}{\sqrt{2\pi}\sigma}e^{-\frac{(t_i-w^{\top}\phi(x_i))^2}{2\sigma^2}}= \\ =n\log \frac{1}{\sqrt{2\pi}\sigma} - \frac{1}{2\sigma^2}\sum_{i=1}^n(t_i-w^{\top}\phi(x_i))^2
$$.

The second term is squared error while $$P(t|X,w,\sigma)$$ is likelihood. So the minimizing of squared error is maximizing of likelihood.




C.M. Bishop  Pattern Recognition and Machine Learning

E.M. Gabidulin, N.I. Pilipchuk Information Theory Lectures
