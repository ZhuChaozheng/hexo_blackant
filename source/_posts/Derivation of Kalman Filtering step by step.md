---
title: Derivation of Kalman Filtering step by step
date: 2019-11-27 13:15:14
tags: article
---
## Basic knowledge

### <a name="Bayes' theorem">Bayes' theorem</a>

$\begin{align}P(x|y)&=\frac{P(x,y)}{P(y)}\tag{1}\end{align}$

$\begin{align}&=\frac{P(x,y)}{\int_{x}^{}P(x,y)dx}\tag{2}\end{align}$

$\begin{align}&=\frac{P(y|x)P(x)}{\int_{x}^{}P(x,y)dx} \tag{3}\end{align}$

Note that, $P(x|y)$ is not only a conditional probability, but a Posterior probability for $x$, which means The probability of occurrence of $x$ after y occurs. $P(x)$ is not only a marginal probability, but a prior probability. $P(y|x)$ has three meanings, first is a conditional probability, and secondly, a Posterior probability for y, last but not least, is called by likelihood function. Also, $P(y)$ has three meanings, first is a marginal probability, and secondly, a prior probability, last but not least, is called by normalized constant.
<!--more-->

### Features of Gaussian distribution

#### <a name="divided equation">Divided equation</a>

Suppose that this distribution is : 

$x=\begin{bmatrix}
x_1\\
x_2
\end{bmatrix}\sim N(\begin{bmatrix}
u_1\\ 
u_2
\end{bmatrix},\begin{bmatrix}\Sigma_{11}&\Sigma_{12}\\ \Sigma_{21}&\Sigma_{22} \end{bmatrix})\tag4{}$

As we all known, a joint distribution can be divided to the multiple of prior probability and conditional probability

$P(x)=P(x_1, x_2)=P(x_1|x_2)P(x_2) \tag{5}$

Then, we can directly derive the result of conditional probability:

$ P(x_1|x_2)\sim N(u_1+\Sigma_{12}\Sigma_{22}^{-1}(x_2-u_2), \Sigma_{11}-\Sigma_{12}\Sigma_{22}^{-1}\Sigma_{21})\tag{6}$

#### <a name="combined equation">Combined equation</a>

if we known conditional probability and prior probability, like the following:

$\begin{align}P(x_2) &= N(u_2, \Sigma_{22})\\P(x_1|x_2) &= N(Hx_2, R)\end{align}$

So, their joint distribution $P(x)$, equation (4) :

$x=\begin{bmatrix}
x_1\\
x_2
\end{bmatrix}\sim N(\begin{bmatrix}
Hu_2\\ 
u_2
\end{bmatrix},\begin{bmatrix}H\Sigma_{22}H^T+R&H\Sigma_{22}\\ \Sigma_{22}H^T&\Sigma_{22} \end{bmatrix})\tag{7}$

#### <a name="Marginalization">Marginalization</a>

if we want to marginalize a joint probability distribution, just remove the corresponding row and column of variable. Specifically, like that:

$$\begin{align}\int_{x_2}P(x_1,x_2)dx_2 &= \int_{x_2}P(x_2|x_1)P(x_1)dx_2\\ &= \int_{x_2}P(x_2|x_1)dx_2P(x_1)\\ &=\int_{x_2}P(x_1,x_2)dx_2\\ &=P(x_1)=N(u_1, \Sigma_{11})=N(\begin{bmatrix}u_1\\ u_2\end{bmatrix}, \begin{bmatrix}\Sigma_{11}& \Sigma_{12}\\ \Sigma_{21}\Sigma_{22}&\Sigma_{22}\end{bmatrix})\tag{8}\end{align}$$

**Since the markdown can not add the delete line above the concrete symbols, you may find the last equation lacks some essential contents.**

## State estimation

For a practical case of state estimation, we often know the initial state $x_0$ and measurement $z_{1:k}$ , then it usually is described by the following linear equations:
$$
\left\{\begin{matrix}
prediction\ \ model:\ x_k=A_k x_{k-1}+w_k\\ 
measurement\ \ model:\ z_k=H_k x_k+v_k
\end{matrix}\right. ,\ where\ w_k\sim N(0,R_k),v_k\sim N(0, Q_k)\tag{9}
$$
where $\ A_k$ is the state transition matrix, $H_k$ is measurement matrix (the transformation matrix used to map state vector parameters into the measurement domain).

Also, we can describe the nature from the figure 1.

![Figure 1: the nature of Kalman, note that it exists some wrongs in symbol $F_k$, please replace it to $A_k$](/img/thenatureofKalman.png)

a robot move forward from initial state $\hat{x}_0$, after a constant time, its state may becomes state $\bar{x}_1$ which be calculated from certain interior nature $F_1$, and as we do not complete believe the state is $\bar{x}_1$, so we slightly make a measurement by another way at the same state, such as ultrasonic, laser lidar, and etc.. it will return to us a concrete value $z_1$. however, each sensor has different measure method, they will offer us some weird unit to express the same mean as the state. So, we should define a argument $H_1$ to map state into measurement domain. Our target is to calculate fused state $\hat{x}_1$, which combined with $\bar{x}_1$ and $z_k$.

### conditional probability

when considering the conditional probability of $P(x_k|x_{k-1})$, it is easy to know $x_{k-1}$ as a concrete value not the variable at this time. therefore, $A_kx_{k-1}$ is also a constant and $x_k=A_k x_{k-1}+w_k$ means that a constant plus a Gaussian distribution. 

$P(x_k|x_{k-1})\sim N(A_kx_{k-1}, R_k)\tag{10}$

similarly, we can easily derive the following equation:

$P(z_k|x_{k})\sim N(H_kx_{k}, Q_k)\tag{11}$

### Kalman Filtering 

For state estimation, the core work is to derive $P(x_{1:k}|z_{1:k},x_0)$. Mostly, we main concern present state  $x_k$, so the core work becomes to derive $P(x_k|z_{1:k}, x_{1:k-1},x_0)$. Moreover, based on Markov Model, the present state $x_k$ is only influenced by last state $x_{k-1}$. Finally, our target is to derive $P(x_k|z_{1:k},x_{k-1},x_0)$.

Now, Let's start a exciting journey.

Firstly, recall that the bayes' theorem.

As we all known, the Kalman Filtering saftifies the nature of Markov, so it can be simply written as the following:

$\begin{align}P(x_k|z_{1:k},x_{k-1:0})=P(x_k|z_{1:k},x_{k-1})\sim N(\hat{u}_k,\hat{\Sigma}_{k})\end{align} \tag{12}$

$\begin{align}&=\frac{P(x_k,z_k|z_{1:k-1},x_{k-1},x_0)}{P(z_k|z_{1:k-1},x_{k,k-1},x_0)}\\&=\frac{P(x_k,z_k|z_{1:k-1},x_{k-1},x_0)}{\int_{x_k}P(x_k,z_k|z_{1:k-1},x_{k-1},x_0)dx_k}\\ &\propto P(x_k,z_k|z_{1:k-1},x_{k-1},x_0)\end{align}\tag{13}$

Now, we have a great idea that as our target is a conditional probability, if we can get the joint probability distribution, we can directly use the [divided equation](#divided equation) to obtain the final result.

So, Let's mainly consider the joint probability distribution.

$\begin{align}P(x_k,z_k|z_{1:k-1},x_{k-1},x_0)=P(z_k|x_k,z_{1:k-1},x_{k-1},x_0)P(x_k|z_{1:k-1},x_{k-1},x_0)\end{align}\tag{14}$

as we all known, $z_k$ is only related to $x_k$, $P(z_k|x_k,z_{1:k-1},x_{k-1},x_0)$ is equal to $P(z_k|x_k)$. Then, continue to derive the equation.(during all the process of derivation, [Bayes' theorem](#Bayes' theorem) will be used frequently)

$=P(z_k|x_k)P(x_k|z_{1:k-1},x_{k-1},x_0)\\=P(z_k|x_k)\int_{x_{k-1}}P(x_k,x_{k-1}|z_{1:k-1},x_0)dx_{k-1}\\=P(z_k|x_k)\int_{x_{k-1}}P(x_k|x_{k-1})P(x_{k-1}|z_{1:k-1},x_0)dx_{k-1}\tag{15}$

sOK, now, Let's try to decompose the above equation to three parts, *measurement* $P(z_k|x_k)$, *prediction*$\ P(x_k|x_{k-1})$, *state distribution of last time*$\ P(x_{k-1}|z_{1:k-1},x_0)$. 

Please carefully note the last part $P(x_{k-1}|z_{1:k-1},x_0)$, do you remember the equation (12), so 

$P(x_{k-1}|z_{1:k-1},x_0)\sim N(\hat{u}_{k-1},\hat{\Sigma}_{k-1})\tag{16}$.再补一个

Listing all the local results, $(10)(11)(16)$:

$\begin{align}P(z_k|x_{k})&\sim N(H_kx_{k}, Q_k)\\P(x_k|x_{k-1})&\sim N(A_kx_{k-1}, R_k)\\P(x_{k-1}|z_{1:k-1},x_0)&\sim N(\hat{u}_{k-1},\hat{\Sigma}_{k-1})\end{align}$

Then, based on [combined equation](#combined equation), we can easily obtain the following equation:

$P(x_k|x_{k-1})P(x_{k-1}|z_{1:k-1},x_0)\sim N(A_kx_{k-1},R_{k})N(\hat{u}_{k-1},\hat{\Sigma}_{k-1})=N(\begin{bmatrix}A_k\hat{u}_{k-1}\\ \hat{u}_{k-1}\end{bmatrix},\begin{bmatrix}A_k\hat{\Sigma}_{k-1}A_{k}^T+R_k&A_{k}\hat\Sigma_{k-1}\\ \hat\Sigma_{k-1}A_k^T&\hat\Sigma_{k-1} \end{bmatrix}) \tag{17}$

Based on [Marginalization](#Marginalization), 

$\int_{x_{k-1}}P(x_k|x_{k-1})P(x_{k-1}|z_{1:k-1},x_0)dx_{k-1}\sim N(A_k\hat{u}_{k-1}, A_k\hat{\Sigma}_{k-1}A_{k}^T+R_k)\tag{18}$

Now, we would like to define another symbol $\bar{u}_k,\ \bar{\Sigma}_k$ to express $N(A_ku_{k-1}, A_k\hat{\Sigma}_{k-1}A_{k}^T+R_k)$, so equation (18) convert to (19):

$\int_{x_{k-1}}P(x_k|x_{k-1})P(x_{k-1}|z_{1:k-1},x_0)dx_{k-1}\sim N(A_k\hat{u}_{k-1}, A_k\hat{\Sigma}_{k-1}A_{k}^T+R_k)=N(\bar{u}_k, \bar{\Sigma}_{k})\tag{19}$

$$\bar{u}_k=A_k\hat{u}_{k-1}\tag{20}$$

$$\bar\Sigma_k=A_k\hat{\Sigma}_{k-1}A_{k}^T+R_k\tag{21}$$

for the equation (15) is equal to the multiple of  equation (11) and (19):

$P(z_k|x_k)\int_{x_{k-1}}P(x_k|x_{k-1})P(x_{k-1}|z_{1:k-1},x_0)dx_{k-1}\sim N(H_kx_{k}, Q_k)N(\bar{u}_k, \bar{\Sigma}_{k})\tag{18}$

based on [combined equation](#combined equation), equation (19) is equal to (20)

$N(\begin{bmatrix}\bar{u}_k\\ H_k\bar{u}_k\end{bmatrix},\ \begin{bmatrix}\bar\Sigma_{k}&\bar\Sigma_{k}H_k^T\\H_{k}\bar\Sigma_{k}&H_k\bar{\Sigma}_{k}H_{k}^T+Q_k\end{bmatrix}) \tag{20}$

So, we have successfully derive the joint probability of state $x_k$ and measurement $z_k$:

$P(x_k,z_k|z_{1:k-1},x_{k-1},x_0)=\begin{bmatrix}x_k\\ z_k\end{bmatrix}\sim N(\begin{bmatrix}\bar{u}_k\\ H_k\bar{u}_k\end{bmatrix},\ \begin{bmatrix}\bar\Sigma_{k}&\bar\Sigma_{k}H_k^T\\H_{k}\bar\Sigma_{k}&H_k\bar{\Sigma}_{k}H_{k}^T+Q_k\end{bmatrix}) \tag{21}$

according to the theorem of equation (13), the last step is to use [divided equation](#divided equation) to derive conditional probability $P(x_k|z_{1:k},x_{k-1},x_0)$. 

$P(x_k|z_{1:k},x_{k-1},x_0)\sim N(\bar{u}_k+\bar{\Sigma}_{k}H_k^T(H_k\bar{\Sigma}_{k}H_{k}^T+Q_k)^{-1}(z_k-H_k\bar{u}_k), \bar{\Sigma}_{k}-\bar{\Sigma}_{k}H_k^T(H_k\bar{\Sigma}_{k}H_{k}^T+Q_k)^{-1}H_k\bar\Sigma_{k}) \tag{22}$

define

 $$K=\bar{\Sigma}_{k}H_k^T(H_k\bar{\Sigma}_{k}H_{k}^T+Q_k)^{-1} \tag{23}$$

 (22) will convert to (24):

$P(x_k|z_{1:k},x_{k-1},x_0)\sim N(\bar{u}_k+K(z_k-H_k\bar{u}_k), \bar{\Sigma}_{k}-KH_k\bar\Sigma_{k}) \tag{24}$

at the start of this section, we define equation (12), so we can easily obtain the following equation:

$$\hat{u}_k=\bar{u}_k+K(z_k-H_k\bar{u}_k) \tag{25}$$

$$\hat\Sigma_k= \bar{\Sigma}_{k}-KH_k\bar\Sigma_{k} \tag{26}$$

Now, let's make a conclusion. Kalman Filtering has five important equation (18), (19), (22), (24), (25). Those equations are listed:

$$\bar{u}_k=A_k\hat{u}_{k-1}\\\bar\Sigma_k=A_k\hat{\Sigma}_{k-1}A_{k}^T+R_k\\K=\bar{\Sigma}_{k}H_k^T(H_k\bar{\Sigma}_{k}H_{k}^T+Q_k)^{-1} \\\hat{u}_k=\bar{u}_k+K(z_k-H_k\bar{u}_k) \\\hat\Sigma_k= \bar{\Sigma}_{k}-KH_k\bar\Sigma_{k} \tag{27}$$



### Extended Kalman Filtering

for a more usual case, the state and measurement equations are not belonged to linear, but the variable of state still meet Gaussian and Markov Model.
$$
\left\{\begin{matrix}
prediction\ model:\ x_k=f(x_{k-1})+w_k\\ 
measurement\ model:\ z_k=h(x_k)+v_k
\end{matrix}\right. ,\ where\ w_k\sim N(0,R_k),v_k\sim N(0, Q_k)\tag{28}
$$

$$
\bar{x}_k=f({x}_{k-1})\\\bar\Sigma_k=F_k\hat{\Sigma}_{k-1}F_{k}^T+R_k\\F_k=\frac{\partial f}{\partial x}|_{x_k}\tag{29}
$$

$$
\Delta z_k=z_k-h(\bar{x}_k)\\S_k=H_k\bar{\Sigma}_kH_k^T+Q_k\\H_k=\frac{\partial h}{\partial x}|_{\bar{x}_k}
$$

#### conclusion

$$
\bar{x}_k=f({x}_{k-1})\\\bar\Sigma_k=F_k\hat{\Sigma}_{k-1}F_{k}^T+R_k\\F_k=\frac{\partial f}{\partial x}|_{x_k}\\K=\bar{\Sigma}_{k}H_k^T(H_k\bar{\Sigma}_{k}H_{k}^T+Q_k)^{-1} \\\hat{u}_k=\bar{u}_k+K(z_k-h(\bar{x}_k)) \\\hat\Sigma_k= \bar{\Sigma}_{k}-KH_k\bar\Sigma_{k}\\H_k=\frac{\partial h}{\partial x}|_{\bar{x}_k}\tag{30}
$$
