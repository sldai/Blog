
## EM Algorithm Derivation and Some Special Examples

### Introduction

We often want to estimate model parameters from observed data, and the most common method is to maximize the maximum likelihood of a statistical model, i.e. maximum likelihood estimation (MLE). But in some cases the data is incomplete, e.g. some labels are missed or some variable cannot be observed, so directly applying MLE can be quite difficult. And Expectation–maximization (EM) algorithm is proposed to maximize the likelihood with these hidden variables.

The idea of the EM algorithm to solve hidden variables is to use a iterative method. We first guess the hidden data (Expectation step), then based on the observed data and guessed hidden data we can maximize the log-likelihood and solve our model parameters (Maximization step).

Next we will see the derivation of it with a simple example.

### Derivation

Suppose we weigh a batch of vehicles that leave the factory, and there two types of cars, which are labeled as $0$ and $1$. And we already know that the weight of each type conform the Gaussian distribution:

$$P(W=w|Y=i) = \mathcal{G}(w;\mu_i,\sigma_i), i\in\{0,1\}$$

So if we have complete data, data=$\{(w_1,i_1),(w_2,i_2),\dots,(w_T,i_T)\}$, we can simply maximize the log-likelihood with iid assumption:

$$\begin{aligned}
\log P(data)&={\sum_{t=1}^{T}}\log{P(W=w_t,Y=i_t)}\\
&={\sum_{t=1}^{T}}\left[\log P(Y=i_t)+\log P(W=w_t|Y=i_t)\right]
\end{aligned}$$

Adding the constrain ${\sum_{i=0}^{1}P(Y=i)=1}$, and using Lagrange multiplier, we can solve it:

$$
\begin{aligned}
P(Y=i)&=\frac{\sum_{t=1}^{T}I(i_t,i)}{T}
\\
 \mu_i&=\frac{\sum_{t=1}^{T}
I(i_t,i) w_t}{\sum_{t=1}^{T}I(i_t,i)}
\\
\sigma^2_i&=\frac{\sum_{t=1}^{T}I(i_t,i) (w_t-\mu_i)^2}{\sum_{t=1}^{T}I(i_t,i)}
\end{aligned}
$$
$I(x,y)=1$, if $x=y$, otherwise 0.

But if all cars are covered with a black cloth, we cannot get the type. The data becomes $\{(w_1),(w_2),\dots,(w_T)\}$. So the log-likelihood changes to:

$$
\begin{aligned}
\log P(data)&={\sum_{t=1}^{T}}\log{P(W=w_t)}\\
&={\sum_{t=1}^{T}}\log{{\sum_{i=0}^{1}}P(W=w_t,Y=i)}
\end{aligned}
$$

Theoretically, we can compute gradient of this function and get maximum. However, you will find it quite difficult, since the sum is embedded in $\log$. If we can extract the sum outside $\log$, it can be much easier. Jensen’s inequality help to do this.

#### Jensen’s inequality

If $f(x)$ is convex and $X$ a random variable then:

$$
E[f(X)]\geq f(E[X])
$$

The equal sign holds if and only if $P(x=E[x])=1$, i.e. $X$ is a constant.

Here I just give an example to help intuitive understanding. Suppose X is discrete and $P(X=x_1)=t, P(X=x_2)=1-t, f(x)$ is convex, we can get the following picture.
![jensen](https://upload.wikimedia.org/wikipedia/commons/c/c7/ConvexFunction.svg)

And if $f(x)$ is concave, $E[f(X)] \leq f(E[X])$.

Back to the log-likelihood. First we introduce a unknown distribution $\tilde{P}_t(Y)$ with $\sum_{i=0}^{1}\tilde{P}_t(Y=i)=1, 0\leq \tilde{P}_t(Y=i)\leq 1$. Given log is concave, apply Jensen's inequality:

$$
\begin{aligned}
\log P(data)&={\sum_{t=1}^{T}}\log{\sum_{i=0}^{1}}\tilde{P}_t(Y=i)\frac{P(W=w_t,Y=i)}{\tilde{P}_t(Y=i)}\\
&={\sum_{t=1}^{T}}\log E\left[\frac{P(W=w_t,Y)}{\tilde{P}(Y)}\right]\\
&\geq {\sum_{t=1}^{T}}E\left[\log \frac{P(W=w_t,Y)}{\tilde{P}(Y)}\right]\\
&\geq {\sum_{t=1}^{T}}{\sum_{i=0}^{1}}\tilde{P}_t(Y=i)\log\frac{P(W=w_t,Y=i)}{\tilde{P}_t(Y=i)}
\end{aligned}
$$

Here we get a lower bound of the log-likelihood. Suppose we initialize all parameters $\mu_i,\sigma_i,P(i)$, if we can continuously increase this lower bound by iterating parameters, the log-likelihood also increases. EM actually uses two steps to increase the lower bound by iteration. 

#### E-step

In E-step, through choosing $\tilde{P}_t(Y=i)$, the lower bound increase to the log-likelihood. From Jensen's inequality, only when $\frac{P(W=w_t,Y)}{\tilde{P}_t(Y)}$ is a constant, the lower bound equals the log-likelihood. So we can get the following result.

$$
\begin{aligned} 
&\because \frac{P(W=w_t,Y=i)}{\tilde{P}_t(Y=i)}=\alpha, i=\{0,1\}\\
& \therefore {\sum_{i=0}^{1}}P(W=w_t,Y=i)=\alpha {\sum_{i=0}^{1}} \tilde{P}_t(Y=i)=\alpha\\
&\therefore \tilde{P}_t(Y=i)= \frac{P(W=w_t,Y=i)}{{\sum_{i=0}^{1}}P(W=w_t,Y=i)}=P(Y=i|W=w_t)
\end{aligned}
$$

So now we can represent the lower bound as:

$$
Q(\theta,\tilde{\theta})={\sum_{t=1}^{T}}{\sum_{i=0}^{1}}P(Y=i|W=w_t;\tilde{\theta})\log\frac{P(W=w_t,Y=i;\theta)}{P(Y=i|W=w_t;\tilde{\theta})}
$$   

$\theta$ and $\tilde{\theta}$ both represent values of parameters $\mu_i,\sigma_i,P(Y=i)$. $\theta$ is fixed in this step. $\tilde{\theta}$ is what we want to change to increase $Q(\theta,\tilde{\theta})$. Clearly if $\tilde{\theta}=\theta$, the lower bound increase to the log-likelihood.

In E-step, we make the expected value of $P(W=w_t,Y=i;\theta)$ as $Q(\theta,\tilde{\theta})$ with $\tilde{\theta}=\theta$. This is why it is called expectation step.

#### M-step

In M-step, we optimize $Q(\theta,\tilde{\theta})$ by adjusting $\theta$ with $\tilde{\theta}$ fixed, and it is much easier to optimize than $P(W=w_t,Y=i;\theta)$.

$$
\begin{aligned}
\theta'&=\underset{\theta}{\arg \max}Q(\theta,\tilde{\theta})
\\
&=\underset{\theta}{\arg \max}{\sum_{t=1}^{T}}{\sum_{i=0}^{1}}P(Y=i|W=w_t;\tilde{\theta})\log\frac{P(W=w_t,Y=i;\theta)}{P(Y=i|W=w_t;\tilde{\theta})}
\\
&=\underset{\theta}{\arg \max}{\sum_{t=1}^{T}}{\sum_{i=0}^{1}}P(Y=i|W=w_t;\tilde{\theta})\log{P(W=w_t,Y=i;\theta)}
\end{aligned}
$$

#### Summary

Now we can make a summary:

Input: observed data $\{(w_1),(w_2),\dots,(w_T)\}$, the form of conditional distribution $P(Y=i|W=w_t;\tilde{\theta})$, the form of joint distribution $P(W=w_t,Y=i;\theta)$, and the maximum number of iteration steps N.

First initialize $\theta$ as $\theta_0$.

For n=1 to N

E-step: $\tilde{\theta}=\theta_{n-1}$, compute $P(Y=i|W=w_t;\theta_{n-1})$.

M-step: 

$$
\begin{aligned}
\theta_n&=\underset{\theta}{\arg \max}Q(\theta,\theta_{n-1})
\\
&=\underset{\theta}{\arg \max}{\sum_{t=1}^{T}}{\sum_{i=0}^{1}}P(Y=i|W=w_t;{\theta_{n-1}})\log{P(W=w_t,Y=i;\theta)}
\end{aligned}
$$

Though the example is simple, but we can generate it to any other model. Just replace $W$ with all observed variable, and replace $Y$ with all hidden variable. 

#### Auxiliary Function

In E-step, we fix model parameters, and optimize the conditional distribution of hidden data. And in M-step, what we do is to fix the distribution of hidden data and optimize the values of model parameters. This idea is called auxiliary functions in mathematics.

$Q(x,y)$ is an auxiliary function for $f(x)$, if it satisfies:

(i) $Q(x,x)=f(x)$

(ii) $Q(x,y)\geq f(x)$

Recall the property of $Q(\theta,\tilde{\theta})$, what we do is to construct an auxiliary function for $-P(W=w_t,Y=i;\theta)$, since:

(i) $-Q(\theta,{\theta})=-P(W=w_t,Y=i;\theta)$

(ii) $-Q(\theta,\tilde{\theta}) \geq -P(W=w_t,Y=i;\theta)$

The update rule of an auxiliary function is $x_{n+1}={\arg \max}_xQ(x,x_{n})$.

A process of optimizing an auxiliary function is showed below. Hope it can help you understand the essence of EM.

![img](https://raw.githubusercontent.com/sldai/online_img/master/auxi_visual.gif)