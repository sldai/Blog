## EM and CM

From previous part I have introduced the derivation of EM algorithm. In this post I will introduce a simpler form of EM algorithm, classification maximization (CM),especially K-means algorithm.

In E step of EM, we need to calculate the expectation of the log likelihood with estimated hidden variables. In CM, we do not calculate the distribution of hidden variables given observed variables, instead we classify them. So the E step is changed to C step. 

Given observed variables $W=w_t$, hidden variables $Y$, estimated parameters $\tilde{\theta}$, in EM we compute $P(Y=i|W=w_t;\tilde{\theta})$, while in CM we compute $y_t=\underset{i}{\arg \max} P(W=w_t|Y=i;\tilde{\theta})$. So we can view CM as a hard assignment, and EM as a soft assignment, since a sample in CM only contributes to one class, while it contributes to all classes in EM.

In M step of CM, we can do the optimization just like we have complete dataset.

### K-means

K-means is actually the CM version of Gaussian Mixture Model (GMM) with assumption of identity covariance and same priors. Denote $\{x_1, x_2, \dots, x_t\}$ as collected points, $\{y_1, y_2, \dots, y_t\}$ as unobserved labels. And $n$ mixtures. 

For Gaussian Mixture Model:

C step:
$$
y_l=\underset{c}{\arg\max}\{ -\frac{1}{2} (x_l-\mu^i_c)^T(\Sigma^i_c)^{-1}(x_l-\mu^i_c)-\frac{1}{2}\log{|\Sigma^i_c|}+\log \pi^i_c\}
$$

M step:
$$
\begin{aligned}
\mu^{i+1}_c&=\frac{\sum_{k=1}^t I(y_k,c)x_k}{\sum_{k=1}^t I(y_k,c)}
\\
\pi^{i+1}_c&=\frac{\sum_{k=1}^t I(y_k,c)}{t}
\\
\Sigma^{i+1}_c&=\frac{\sum_{k=1}^t I(y_k,c)(x_k-\mu^{i+1}_c)^T(x_k-\mu^{i+1}_c)}{\sum_{k=1}^t I(y_k,c)}
\end{aligned}
$$

For K-means, the covariances are set to $I$, priors are set to $\frac{1}{n}$.

C step:
$$
y_l=\underset{c}{\arg\max}\{ ||x_l-\mu^i_c||_2\}
$$

M step:
$$
\begin{aligned}
\mu^{i+1}_c&=\frac{\sum_{k=1}^t I(y_k,c)x_k}{\sum_{k=1}^t I(y_k,c)}
\end{aligned}
$$

Since K-means do not actually optimize the log likelihood with hidden variables, so its performance is lower than GMM algorithm. But due to its simple calculation, when doing clustering, we can first conduct K-means to get a primary result, which then can be used to initialize parameters of GMM. 