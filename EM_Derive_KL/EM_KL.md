## EM algorithm Convergence Proof with KL Divergence

In my previous post, I introduce the derivation of EM algorithm and convergence proof via defining its auxiliary function. Here I will introduce another method to prove the convergence of EM. 

First let us denote $X, Z$ as the observed variable and hidden variable, and denote $D$ as collected iid samples, $\theta$ as the parameters.

$$
\begin{aligned}
P_{X,Z}(D,z;\theta)&=P_{Z|X}(z|D;\theta)P_X(D;\theta)
\\
\log P_X(D;\theta)&=\log P_{X,Z}(D,z;\theta)-\log P_{Z|X}(z|D;\theta)
\end{aligned}
$$

Taking expectations on both sides and using the fact that the left hand side does not depend on Z.

$$
\begin{aligned}
\log P_X(D;\theta)&=E_{Z|X;\theta^n}(\log P_{X,Z}(D,z;\theta))-E_{Z|X;\theta^n}(\log P_{Z|X}(z|D;\theta))
\\
&=Q(\theta,\theta^n)+H(\theta,\theta^n)
\end{aligned}
$$
where Q function is what EM algorithm actually optimize, and Q is the lower bound of log likelihood. Note that:

$$
\begin{aligned}
H(\theta,\theta^n)&=-E_{Z|X;\theta^n}(\log P_{Z|X}(z|D;\theta))
\\
&=-\int P_{Z|X}(z|D;\theta^n) \log P_{Z|X}(z|D;\theta) dz
\\
&=-\int p_n(z) \log p(z) dz
\\
&=KL[p_n||p]+H[p_n]
\end{aligned}
$$
where $KL[p||q]$ is the Kullback-Leibler divergence between $p$ and $q$, and $H[p]$ the entropy of $p$ itself. So $H(\theta,\theta^n) \geq 0, \log P_X(D;\theta) \geq H(\theta,\theta^n)$

To prove 

$$
Q(\theta,\tilde{\theta})={\sum_{t=1}^{T}}{\sum_{i=0}^{1}}P(Y=i|W=w_t;\tilde{\theta})\log\frac{P(W=w_t,Y=i;\theta)}{P(Y=i|W=w_t;\tilde{\theta})}
$$

