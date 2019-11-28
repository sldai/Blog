  
##  EM Algorithm Derivation and Auxiliary function
  
  
###  Introduction
  
  
We often want to estimate model parameters from observed data, and the most common method is to maximize the maximum likelihood of a statistical model, i.e. maximum likelihood estimation (MLE). But in some cases the data is incomplete, e.g. some labels are missed or some variable cannot be observed, so directly applying MLE can be quite difficult. And Expectation–maximization (EM) algorithm is proposed to maximize the likelihood with these hidden variables.
  
The idea of the EM algorithm to solve hidden variables is to use a iterative method. We first guess the hidden data (Expectation step), then based on the observed data and guessed hidden data we can maximize the log-likelihood and solve our model parameters (Maximization step).
  
Next we will see the derivation of it with a simple example.
  
###  Derivation
  
  
Suppose we weigh a batch of vehicles that leave the factory, and there two types of cars, which are labeled as <img src="https://latex.codecogs.com/gif.latex?0"/> and <img src="https://latex.codecogs.com/gif.latex?1"/>. And we already know that the weight of each type conform the Gaussian distribution:
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?P(W=w|Y=i)%20=%20&#x5C;mathcal{G}(w;&#x5C;mu_i,&#x5C;sigma_i),%20i&#x5C;in&#x5C;{0,1&#x5C;}"/></p>  
  
  
So if we have complete data, data=<img src="https://latex.codecogs.com/gif.latex?&#x5C;{(w_1,i_1),(w_2,i_2),&#x5C;dots,(w_T,i_T)&#x5C;}"/>, we can simply maximize the log-likelihood with iid assumption:
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;begin{aligned}&#x5C;log%20P(data)&amp;={&#x5C;sum_{t=1}^{T}}&#x5C;log{P(W=w_t,Y=i_t)}&#x5C;&#x5C;&amp;={&#x5C;sum_{t=1}^{T}}&#x5C;left[&#x5C;log%20P(Y=i_t)+&#x5C;log%20P(W=w_t|Y=i_t)&#x5C;right]&#x5C;end{aligned}"/></p>  
  
  
Adding the constrain <img src="https://latex.codecogs.com/gif.latex?{&#x5C;sum_{i=0}^{1}P(Y=i)=1}"/>, and using Lagrange multiplier, we can solve it:
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;begin{aligned}P(Y=i)&amp;=&#x5C;frac{&#x5C;sum_{t=1}^{T}I(i_t,i)}{T}&#x5C;&#x5C;%20&#x5C;mu_i&amp;=&#x5C;frac{&#x5C;sum_{t=1}^{T}I(i_t,i)%20w_t}{&#x5C;sum_{t=1}^{T}I(i_t,i)}&#x5C;&#x5C;&#x5C;sigma^2_i&amp;=&#x5C;frac{&#x5C;sum_{t=1}^{T}I(i_t,i)%20(w_t-&#x5C;mu_i)^2}{&#x5C;sum_{t=1}^{T}I(i_t,i)}&#x5C;end{aligned}"/></p>  
  
<img src="https://latex.codecogs.com/gif.latex?I(x,y)=1"/>, if <img src="https://latex.codecogs.com/gif.latex?x=y"/>, otherwise 0.
  
But if all cars are covered with a black cloth, we cannot get the type. The data becomes <img src="https://latex.codecogs.com/gif.latex?&#x5C;{(w_1),(w_2),&#x5C;dots,(w_T)&#x5C;}"/>. So the log-likelihood changes to:
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;begin{aligned}&#x5C;log%20P(data)&amp;={&#x5C;sum_{t=1}^{T}}&#x5C;log{P(W=w_t)}&#x5C;&#x5C;&amp;={&#x5C;sum_{t=1}^{T}}&#x5C;log{{&#x5C;sum_{i=0}^{1}}P(W=w_t,Y=i)}&#x5C;end{aligned}"/></p>  
  
  
Theoretically, we can compute gradient of this function and get maximum. However, you will find it quite difficult, since the sum is embedded in <img src="https://latex.codecogs.com/gif.latex?&#x5C;log"/>. If we can extract the sum outside <img src="https://latex.codecogs.com/gif.latex?&#x5C;log"/>, it can be much easier. Jensen’s inequality help to do this.
  
####  Jensen’s inequality
  
  
If <img src="https://latex.codecogs.com/gif.latex?f(x)"/> is convex and <img src="https://latex.codecogs.com/gif.latex?X"/> a random variable then:
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?E[f(X)]&#x5C;geq%20f(E[X])"/></p>  
  
  
The equal sign holds if and only if <img src="https://latex.codecogs.com/gif.latex?P(x=E[x])=1"/>, i.e. <img src="https://latex.codecogs.com/gif.latex?X"/> is a constant.
  
Here I just give an example to help intuitive understanding. Suppose X is discrete and <img src="https://latex.codecogs.com/gif.latex?P(X=x_1)=t,%20P(X=x_2)=1-t,%20f(x)"/> is convex, we can get the following picture.
![jensen](https://upload.wikimedia.org/wikipedia/commons/c/c7/ConvexFunction.svg )
  
And if <img src="https://latex.codecogs.com/gif.latex?f(x)"/> is concave, <img src="https://latex.codecogs.com/gif.latex?E[f(X)]%20&#x5C;leq%20f(E[X])"/>.
  
Back to the log-likelihood. First we introduce a unknown distribution <img src="https://latex.codecogs.com/gif.latex?&#x5C;tilde{P}_t(Y)"/> with <img src="https://latex.codecogs.com/gif.latex?&#x5C;sum_{i=0}^{1}&#x5C;tilde{P}_t(Y=i)=1,%200&#x5C;leq%20&#x5C;tilde{P}_t(Y=i)&#x5C;leq%201"/>. Given log is concave, apply Jensen's inequality:
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;begin{aligned}&#x5C;log%20P(data)&amp;={&#x5C;sum_{t=1}^{T}}&#x5C;log{&#x5C;sum_{i=0}^{1}}&#x5C;tilde{P}_t(Y=i)&#x5C;frac{P(W=w_t,Y=i)}{&#x5C;tilde{P}_t(Y=i)}&#x5C;&#x5C;&amp;={&#x5C;sum_{t=1}^{T}}&#x5C;log%20E&#x5C;left[&#x5C;frac{P(W=w_t,Y)}{&#x5C;tilde{P}(Y)}&#x5C;right]&#x5C;&#x5C;&amp;&#x5C;geq%20{&#x5C;sum_{t=1}^{T}}E&#x5C;left[&#x5C;log%20&#x5C;frac{P(W=w_t,Y)}{&#x5C;tilde{P}(Y)}&#x5C;right]&#x5C;&#x5C;&amp;&#x5C;geq%20{&#x5C;sum_{t=1}^{T}}{&#x5C;sum_{i=0}^{1}}&#x5C;tilde{P}_t(Y=i)&#x5C;log&#x5C;frac{P(W=w_t,Y=i)}{&#x5C;tilde{P}_t(Y=i)}&#x5C;end{aligned}"/></p>  
  
  
Here we get a lower bound of the log-likelihood. Suppose we initialize all parameters <img src="https://latex.codecogs.com/gif.latex?&#x5C;mu_i,&#x5C;sigma_i,P(i)"/>, if we can continuously increase this lower bound by iterating parameters, the log-likelihood also increases. EM actually uses two steps to increase the lower bound by iteration. 
  
####  E-step
  
  
In E-step, through choosing <img src="https://latex.codecogs.com/gif.latex?&#x5C;tilde{P}_t(Y=i)"/>, the lower bound increase to the log-likelihood. From Jensen's inequality, only when <img src="https://latex.codecogs.com/gif.latex?&#x5C;frac{P(W=w_t,Y)}{&#x5C;tilde{P}_t(Y)}"/> is a constant, the lower bound equals the log-likelihood. So we can get the following result.
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;begin{aligned}%20&amp;&#x5C;because%20&#x5C;frac{P(W=w_t,Y=i)}{&#x5C;tilde{P}_t(Y=i)}=&#x5C;alpha,%20i=&#x5C;{0,1&#x5C;}&#x5C;&#x5C;&amp;%20&#x5C;therefore%20{&#x5C;sum_{i=0}^{1}}P(W=w_t,Y=i)=&#x5C;alpha%20{&#x5C;sum_{i=0}^{1}}%20&#x5C;tilde{P}_t(Y=i)=&#x5C;alpha&#x5C;&#x5C;&amp;&#x5C;therefore%20&#x5C;tilde{P}_t(Y=i)=%20&#x5C;frac{P(W=w_t,Y=i)}{{&#x5C;sum_{i=0}^{1}}P(W=w_t,Y=i)}=P(Y=i|W=w_t)&#x5C;end{aligned}"/></p>  
  
  
So now we can represent the lower bound as:
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?Q(&#x5C;theta,&#x5C;tilde{&#x5C;theta})={&#x5C;sum_{t=1}^{T}}{&#x5C;sum_{i=0}^{1}}P(Y=i|W=w_t;&#x5C;tilde{&#x5C;theta})&#x5C;log&#x5C;frac{P(W=w_t,Y=i;&#x5C;theta)}{P(Y=i|W=w_t;&#x5C;tilde{&#x5C;theta})}"/></p>  
  
  
<img src="https://latex.codecogs.com/gif.latex?&#x5C;theta"/> and <img src="https://latex.codecogs.com/gif.latex?&#x5C;tilde{&#x5C;theta}"/> both represent values of parameters <img src="https://latex.codecogs.com/gif.latex?&#x5C;mu_i,&#x5C;sigma_i,P(Y=i)"/>. <img src="https://latex.codecogs.com/gif.latex?&#x5C;theta"/> is fixed in this step. <img src="https://latex.codecogs.com/gif.latex?&#x5C;tilde{&#x5C;theta}"/> is what we want to change to increase <img src="https://latex.codecogs.com/gif.latex?Q(&#x5C;theta,&#x5C;tilde{&#x5C;theta})"/>. Clearly if <img src="https://latex.codecogs.com/gif.latex?&#x5C;tilde{&#x5C;theta}=&#x5C;theta"/>, the lower bound increase to the log-likelihood.
  
In E-step, we make the expected value of <img src="https://latex.codecogs.com/gif.latex?P(W=w_t,Y=i;&#x5C;theta)"/> as <img src="https://latex.codecogs.com/gif.latex?Q(&#x5C;theta,&#x5C;tilde{&#x5C;theta})"/> with <img src="https://latex.codecogs.com/gif.latex?&#x5C;tilde{&#x5C;theta}=&#x5C;theta"/>. This is why it is called expectation step.
  
####  M-step
  
  
In M-step, we optimize <img src="https://latex.codecogs.com/gif.latex?Q(&#x5C;theta,&#x5C;tilde{&#x5C;theta})"/> by adjusting <img src="https://latex.codecogs.com/gif.latex?&#x5C;theta"/> with <img src="https://latex.codecogs.com/gif.latex?&#x5C;tilde{&#x5C;theta}"/> fixed, and it is much easier to optimize than <img src="https://latex.codecogs.com/gif.latex?P(W=w_t,Y=i;&#x5C;theta)"/>.
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;begin{aligned}&#x5C;theta&#x27;&amp;=&#x5C;underset{&#x5C;theta}{&#x5C;arg%20&#x5C;max}Q(&#x5C;theta,&#x5C;tilde{&#x5C;theta})&#x5C;&#x5C;&amp;=&#x5C;underset{&#x5C;theta}{&#x5C;arg%20&#x5C;max}{&#x5C;sum_{t=1}^{T}}{&#x5C;sum_{i=0}^{1}}P(Y=i|W=w_t;&#x5C;tilde{&#x5C;theta})&#x5C;log&#x5C;frac{P(W=w_t,Y=i;&#x5C;theta)}{P(Y=i|W=w_t;&#x5C;tilde{&#x5C;theta})}&#x5C;&#x5C;&amp;=&#x5C;underset{&#x5C;theta}{&#x5C;arg%20&#x5C;max}{&#x5C;sum_{t=1}^{T}}{&#x5C;sum_{i=0}^{1}}P(Y=i|W=w_t;&#x5C;tilde{&#x5C;theta})&#x5C;log{P(W=w_t,Y=i;&#x5C;theta)}&#x5C;end{aligned}"/></p>  
  
  
####  Summary
  
  
Now we can make a summary:
  
Input: observed data <img src="https://latex.codecogs.com/gif.latex?&#x5C;{(w_1),(w_2),&#x5C;dots,(w_T)&#x5C;}"/>, the form of conditional distribution <img src="https://latex.codecogs.com/gif.latex?P(Y=i|W=w_t;&#x5C;tilde{&#x5C;theta})"/>, the form of joint distribution <img src="https://latex.codecogs.com/gif.latex?P(W=w_t,Y=i;&#x5C;theta)"/>, and the maximum number of iteration steps N.
  
First initialize <img src="https://latex.codecogs.com/gif.latex?&#x5C;theta"/> as <img src="https://latex.codecogs.com/gif.latex?&#x5C;theta_0"/>.
  
For n=1 to N
  
E-step: <img src="https://latex.codecogs.com/gif.latex?&#x5C;tilde{&#x5C;theta}=&#x5C;theta_{n-1}"/>, compute <img src="https://latex.codecogs.com/gif.latex?P(Y=i|W=w_t;&#x5C;theta_{n-1})"/>.
  
M-step: 
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;begin{aligned}&#x5C;theta_n&amp;=&#x5C;underset{&#x5C;theta}{&#x5C;arg%20&#x5C;max}Q(&#x5C;theta,&#x5C;theta_{n-1})&#x5C;&#x5C;&amp;=&#x5C;underset{&#x5C;theta}{&#x5C;arg%20&#x5C;max}{&#x5C;sum_{t=1}^{T}}{&#x5C;sum_{i=0}^{1}}P(Y=i|W=w_t;{&#x5C;theta_{n-1}})&#x5C;log{P(W=w_t,Y=i;&#x5C;theta)}&#x5C;end{aligned}"/></p>  
  
  
Though the example is simple, but we can generate it to any other model. Just replace <img src="https://latex.codecogs.com/gif.latex?W"/> with all observed variable, and replace <img src="https://latex.codecogs.com/gif.latex?Y"/> with all hidden variable. 
  
####  Auxiliary Function
  
  
In E-step, we fix model parameters, and optimize the conditional distribution of hidden data. And in M-step, what we do is to fix the distribution of hidden data and optimize the values of model parameters. This idea is called auxiliary functions in mathematics.
  
<img src="https://latex.codecogs.com/gif.latex?Q(x,y)"/> is an auxiliary function for <img src="https://latex.codecogs.com/gif.latex?f(x)"/>, if it satisfies:
  
(i) <img src="https://latex.codecogs.com/gif.latex?Q(x,x)=f(x)"/>
  
(ii) <img src="https://latex.codecogs.com/gif.latex?Q(x,y)&#x5C;geq%20f(x)"/>
  
Recall the property of <img src="https://latex.codecogs.com/gif.latex?Q(&#x5C;theta,&#x5C;tilde{&#x5C;theta})"/>, what we do is to construct an auxiliary function for <img src="https://latex.codecogs.com/gif.latex?-P(W=w_t,Y=i;&#x5C;theta)"/>, since:
  
(i) <img src="https://latex.codecogs.com/gif.latex?-Q(&#x5C;theta,{&#x5C;theta})=-P(W=w_t,Y=i;&#x5C;theta)"/>
  
(ii) <img src="https://latex.codecogs.com/gif.latex?-Q(&#x5C;theta,&#x5C;tilde{&#x5C;theta})%20&#x5C;geq%20-P(W=w_t,Y=i;&#x5C;theta)"/>
  
The update rule of an auxiliary function is <img src="https://latex.codecogs.com/gif.latex?x_{n+1}={&#x5C;arg%20&#x5C;max}_xQ(x,x_{n})"/>.
  
A process of optimizing an auxiliary function is showed below. Hope it can help you understand the essence of EM.
  
![img](auxi_visual.gif )
  