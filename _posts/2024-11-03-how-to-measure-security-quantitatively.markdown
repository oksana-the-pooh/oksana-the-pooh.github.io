---
layout: post
title:  "How to Measure Security"
date:   2025-04-30 12:47:00 +0300
categories: cryptography information_theory probability_theory
use_math: true
---
Can we measure security quantitatively?

We use a lot of cryptographic tools not even noticing it. Almost every website exploring uses HTTPS to establish secure connection. In order to establish it a web browser and a website server must agree on which cipher to use for data encyption, which Message Authentication Code algorithm to use for a message integrity checking. Also a browser authenticates web site certificate using digital signature. When you pay by a credict card using PayPass/PayWare the card signs a transaction by its digital signature. All this tools serve to guarantee a security. But what does security mean and can we see that quantitatively?

### How to approach the question

For most of its history ctyptography was like a game where each side trying to outwit the other one. An encryption scheme or cipher was considered to be secure unless someone can successfully attack it. Then cryptographers developed some ad hoc defence and waited for a next attakers' move. Nowdays cryptography still doesn't look like the true science with clear and precise system of concepts, proofs and so on. Thanks to Claude Shannon there is a benchmark of a security. 

Let's consider every message $$m$$ to be sent securelly as a sample of some distribution $$P(M)$$. By intercepting a message $$m$$ an attacker got some message $$w$$, which in turn is a sample of some distribution $$P(W)$$. According to Shannon the link between $$P(MW)$$ and $$P(M)P(W)$$ is the point.

### How to measure

So we can consider all possible distances between distributions. Namely,

- mutual information $$I(M; W) = D_{KL}(P(MW)\|P(W)P(M))$$
- variation distance $$\Delta(P(MW), P(M)P(W))$$, where $$\Delta(P(X),Q(X)) = \sum_{x \in X} \vert P(X=x) - Q(X=x)\vert$$
- information density $$i(M; W) = \ln\frac{P(WM)}{P(W)P(M)}$$
- mutual information per symbol $$\frac{I(W; M)}{n}$$
- variation distance per symbol $$\frac{\Delta(P(WM),P(W)P(M))}{n}$$
- information density per symbol $$\frac{i(W; M)}{n}$$


where $$n$$ is a length of a message. Why is the length important? Imagine someone eavesdrops you through the window from the outside. If you said only one word the eavesdropper could easily catch it if it wasn't too noisy outside at that time. But if you told long detailed story the eavesdropper might not catch a meaning of the story, because he missed some important words due to street noise.

### Which measure to choose

To choose the best distance or just order them we need to determine comparison operator.

They say $$\delta_{n}^{(1)}$$ is stronger than $$\delta_{n}^{(2)}$$  ($$\delta_n^{(2)}\preceq \delta_n^{(1)}$$) if convergence in probability to zero of $$\delta_{n}^{(1)}$$ results in convergence in probability to zero of $$\delta_{n}^{(2)}$$, i.e.
$$
\forall \epsilon >0\; \lim\limits_{n\rightarrow \infty}P(|\delta_n^{(1)}|>\epsilon)=0 \Rightarrow \lim\limits_{n\rightarrow \infty}P(|\delta_n^{(2)}|>\epsilon)=0.
$$

So the right order is
$$
\rlap{\overbrace{\phantom{\frac{i(M;X)}{n} \preceq \frac{\Delta(P(WM),P(W)P(M))}{n} \preceq \frac{I(W;M)}{n} } }^{\text{(1) M.S. Pinsker 1960}}}
\frac{i(M;X)}{n} \preceq \frac{\Delta(P(WM),P(W)P(M))}{n} 
     \preceq \underbrace{\frac{I(W;M)}{n} \preceq i(W;M)}_{\text{(2) M. Bloch, J.N. Laneman 2008}} 
\preceq \Delta(P(WM),P(W)P(M)) \preceq I(W;M) \llap{\overbrace{\phantom{i(M;X) \preceq \Delta(P(WM),P(W)P(M)) \preceq I(W;M)}}^{\text{(1) M.S. Pinsker 1960}}}
$$

Let's look at (1) closely.

$$\begin{align*}
I(W;M) &= \int\limits_{WM}P(WM)\ln\frac{P(WM)}{P(W)P(M)}\;dwdm = \int\limits_{WM}i(W;M)P(WM)\;dwdm \\
&= \int\limits_{i(W;M)<0} i(W;M)P(W;X)\;dwdm + \int\limits_{i(W;M)>0}i(W;M)P(W;X)\;dwdm
\end{align*}$$
$$\begin{align*}
E(\vert i(W;M)\vert) = \int\limits_{WM}\vert i(W;M)\vert P(WM)\;dwdm &= -\int\limits_{i(W;M)<0} i(W;M)P(WX)\;dwdm + \int\limits_{i(W;M)>0}i(W;M)P(WX)\;dwdm \\
& = I(W;M) -2\int\limits_{i(W;M)<0}i(W;M)P(WM)\;dwdm
\end{align*}$$
$$\begin{align*}
\int\limits_{i(W;M)<0}i(W;M)P(WM)\;dwdm &= \int\limits_{i(W;M)<0}P(WM)\ln\frac{P(WM)}{P(W)P(M)}\;dwdm\\ 
&= \int\limits_{i(W;M)<0}\frac{P(WM)}{P(W)P(M)}P(W)P(M)\ln\frac{P(WM)}{P(W)P(M)}\;dwdm
\end{align*}$$

Let's consider function $$f(x)=x\ln x$$. 

$$\begin{align*}f'(x)&=\ln x+1=0 \Rightarrow x=\frac{1}{e}\\
f''(x)&=\frac{1}{x}\\
f''(\frac{1}{e}) &=e>0 \Rightarrow f(x) \geq -\frac{1}{e}\;\;\;\forall x > 0
\end{align*}$$

Then

$$\begin{align*}\int\limits_{i(W;M)<0}i(W;M)P(WM)\;dwdm \geq -\int\limits_{WM}\frac{1}{e}P(W)P(M)\;dwdm \geq -\frac{1}{e}\end{align*}$$
and 
$$\begin{align*}E(\vert i(W;M)\vert) = I(W;M) -2\int\limits_{i(W;M)<0}i(W;M)P(WM)\;dwdm \leq I(W;M) +\frac{2}{e}\end{align*}$$

Pinsker proved more strong inequation for small values of $$I(W;M)$$ $$E(\vert i(W;M)\vert) \leq I(W;M) + c\sqrt{I(W;M)}$$, where $$c$$ is some constant.

$$\begin{align*}
\Delta(P(WM),P(W)P(M)) &= \int\limits_{WM}\vert P(WM)-P(W)P(M)\vert\;dwdm\\ &= \int\limits_{P(WM)>=P(W)P(M)} (P(WM)- P(W)P(M))\;dwdm\\ 
&+ \int\limits_{P(WM)<P(W)P(M)} (P(W)P(M)-P(WM))\;dwdm
\end{align*}$$

Let's notice that
$$\begin{align*}
&\int\limits_{P(WM)>=P(W)P(M)} (P(WM)- P(W)P(M))\;dwdm - \int\limits_{P(WM)<P(W)P(M)} (P(W)P(M)-P(WM))\;dwd \\
&=\left(\int\limits_{P(WM)>=P(W)P(M)}P(WM)\;dwdm+\int\limits_{P(WM)<P(W)P(M)}P(WM)\;dwdm\right)\\
&-\left(\int\limits_{P(WM)>=P(W)P(M)} P(W)P(M)\;dwdm+\int\limits_{P(WM)<P(W)P(M)} P(W)P(M)\;dwdm\right)\\
&=1-1=0
\end{align*}$$
Then 
$$\begin{align*}\Delta(P(WM),P(W)P(M)) &= 2\int\limits_{P(WM)>=P(W)P(M)} (P(WM)- P(W)P(M))\;dwdm\\
&=2\int\limits_{P(WM)>=P(W)P(M)}(1-\frac{P(W)P(M)}{P(WM)}) P(WM)\;dwdm\\ &= 2\int\limits_{P(WM)>=P(W)P(M)}(1-\frac{1}{\frac{P(WM)}{P(W)P(M)}})P(WM)\;dwdm
\end{align*}$$

Recall some calculus in order to estimate the above result. $$e^x=\sum\limits_{n=0}^{\infty}(-1)^n\frac{x^n}{n!}$$. The series converges for all $$x$$. So

$$\begin{align*}
e^x &\geq 1+x\;\;\;\forall x\\
e^{-y} &\geq 1-y,\;\;\;-y\leftarrow x\\
e^{-\ln u} &\geq 1-\ln u,\;\;\;\ln u\leftarrow y\\
\frac{1}{u} &\geq 1 -\ln u\\
\ln u &\geq 1-\frac{1}{u}
\end{align*}$$

Then
$$\begin{align*}
\Delta(P(WM),P(W)P(M)) &\leq 2\int\limits_{P(WM)>=P(W)P(M)}\ln(\frac{P(WM)}{P(W)P(M)})P(WM)\;dwdm\\
&\leq 2\int\limits_{WM}\vert \ln(\frac{P(WM)}{P(W)P(M)})\vert P(WM)\;dwdm \\ &= 2\int\limits_{WM}\vert i(W;M)\vert P(WM)\;dwdm = 2E(\vert i(W;M)\vert) = 2I(W;M)+c\sqrt{I(W;M)}
\end{align*}$$

So, if $$\lim\limits_{n\rightarrow \infty}I(W_n;M_n)=0 \Rightarrow \lim\limits_{n\rightarrow \infty}\Delta(P(W_nM_n),P(W_n)P(M_n))=0$$ and $$\Delta(P(WM),P(W)P(M)) \preceq I(W;M)$$ is clear now.

To deal with $$i(W;M) \preceq \Delta(P(WM),P(W)P(M))$$ we need consider $$P(\vert i(W;M)\vert > \epsilon)$$.

$$\vert i(W;M)\vert = \vert \ln \frac{P(WM)}{P(W)P(M)}\vert > \epsilon$$

$$\begin{align*}
\ln \frac{P(WM)}{P(W)P(M)} > \epsilon &\;\;\;\;\;\text{or}\;\;\;\;\;\ln \frac{P(WM)}{P(W)P(M)} < -\epsilon\\
\ln \frac{P(WM)}{P(W)P(M)} > \epsilon \geq \ln (1+\epsilon) &\;\;\;\;\;\text{or}\;\;\;\;\; 1-\frac{1}{\frac{P(WM)}{P(W)P(M)}} \leq \ln \frac{P(WM)}{P(W)P(M)} < -\epsilon\\
\frac{P(WM)}{P(W)P(M)} > 1+\epsilon &\;\;\;\;\;\text{or}\;\;\;\;\; 1-\frac{1}{\frac{P(WM)}{P(W)P(M)}} < -\epsilon\\
\frac{P(W)P(M)}{P(WM)} < \frac{1}{1+\epsilon} &\;\;\;\;\;\text{or}\;\;\;\;\; 1-\frac{1}{\frac{P(WM)}{P(W)P(M)}} < -\epsilon\\
\frac{1}{\frac{P(WM)}{P(W)P(M)}} < \frac{1}{1+\epsilon} &\;\;\;\;\;\text{or}\;\;\;\;\; 1-\frac{1}{\frac{P(WM)}{P(W)P(M)}} < -\epsilon\\
1-\frac{1}{\frac{P(WM)}{P(W)P(M)}} > 1- \frac{1}{1+\epsilon} &\;\;\;\;\;\text{or}\;\;\;\;\; 1-\frac{1}{\frac{P(WM)}{P(W)P(M)}} < -\epsilon\\
1-\frac{1}{\frac{P(WM)}{P(W)P(M)}} > \frac{\epsilon}{1+\epsilon} &\;\;\;\;\;\text{or}\;\;\;\;\; 1-\frac{1}{\frac{P(WM)}{P(W)P(M)}} < -\epsilon\\
\left \vert 1-\frac{1}{\frac{P(WM)}{P(W)P(M)}} \right \vert &> \frac{\epsilon}{1+\epsilon}
\end{align*}$$

And

$$
P(\vert i(W;M)\vert > \epsilon) \leq P\left (\left \vert 1-\frac{1}{\frac{P(WM)}{P(W)P(M)}} \right \vert > \frac{\epsilon}{1+\epsilon}\right )
$$

$$\begin{align*}
\Delta(P(WM),P(W)P(M)) &= \int\limits_{WM} \vert P(WM) -P(W)P(M)\vert \;dwdm = \int\limits_{WM}\left \vert 1-\frac{1}{\frac{P(WM)}{P(W)P(M)}}\right \vert P(WM)\;dwdm\\
&\geq \int\limits_{\vert i(W;M)\vert > \epsilon}\left \vert 1-\frac{1}{\frac{P(WM)}{P(W)P(M)}}\right \vert P(WM)\;dwdm \\
&\geq \frac{\epsilon}{1+\epsilon}\int\limits_{\vert i(W;M)\vert > \epsilon} P(WM)\;dwdm = \frac{\epsilon}{1+\epsilon}P(\vert i(W;M)\vert > \epsilon)
\end{align*}$$

$$P(\vert i(W;M)\vert > \epsilon) \leq \frac{1+\epsilon}{\epsilon}\Delta(P(WM),P(W)P(M))$$

Finally

$$\lim\limits_{n\rightarrow \infty}\Delta(P(W_nM_n),P(W_n)P(M_n))=0 \Rightarrow \lim\limits_{n\rightarrow \infty}P(\vert i(W_n;M_n)\vert > \epsilon)=0$$.

What means $$i(W;M)\preceq \Delta(P(WM),P(W)P(M))$$.

Well, $$i(W;M)\preceq \Delta(P(WM),P(W)P(M)) \preceq I(W;M)$$ is done. 

There is only one relation left $$\frac{I(W;M)}{n}\preceq i(W;M)$$.

$$\begin{align*}
\frac{I(W;M)}{n} = E\left (\frac{i(W;M)}{n}\right ) &=\int\limits_{i(W;M)\leq -\epsilon}\frac{i(W;M)}{n}P(WM)\;dwdm +\int\limits_{-\epsilon < i(W;M)\leq \epsilon}\frac{i(W;M)}{n}P(WM)\;dwdm\\
&+\int\limits_{\epsilon < i(W;M) \leq \ln\vert \mathcal{M}\vert}\frac{i(W;M)}{n}P(WM)\;dwdm + \int\limits_{i(W;M) > \ln\vert \mathcal{M}\vert}\frac{i(W;M)}{n}P(WM)\;dwdm\\
&= \int\limits_{i(W;M)\leq -\epsilon}\frac{i(W;M)}{n}P(WM)\;dwdm +\int\limits_{-\epsilon < i(W;M)\leq \epsilon}\frac{i(W;M)}{n}P(WM)\;dwdm\\
&+\int\limits_{\epsilon < i(W;M) \leq \ln\vert \mathcal{M}\vert}\frac{1}{n}\ln\frac{P(WM)}{P(W)P(M)}P(WM)\;dwdm + \int\limits_{i(W;M) > \ln\vert \mathcal{M}\vert}\frac{i(W;M)}{n}P(WM)\;dwdm\\
&\leq \int\limits_{i(W;M)\leq -\epsilon}\frac{i(W;M)}{n}P(WM)\;dwdm +\int\limits_{-\epsilon < i(W;M)\leq \epsilon}\frac{i(W;M)}{n}P(WM)\;dwdm\\
&+\int\limits_{\epsilon < i(W;M) \leq \ln\vert \mathcal{M}\vert}\frac{1}{n}\ln\frac{P(W)}{P(W)P(M)}P(WM)\;dwdm + \int\limits_{i(W;M) > \ln\vert \mathcal{M}\vert}\frac{i(W;M)}{n}P(WM)\;dwdm\\
&= \int\limits_{i(W;M)\leq -\epsilon}\frac{i(W;M)}{n}P(WM)\;dwdm +\int\limits_{-\epsilon < i(W;M)\leq \epsilon}\frac{i(W;M)}{n}P(WM)\;dwdm\\
&+\int\limits_{\epsilon < i(W;M) \leq \ln\vert \mathcal{M}\vert}\frac{1}{n}\ln\frac{1}{P(M)}P(WM)\;dwdm + \int\limits_{i(W;M) > \ln\vert \mathcal{M}\vert}\frac{i(W;M)}{n}P(WM)\;dwdm\\
& \leq \frac{\epsilon}{n}\int\limits_{-\epsilon < i(W;M)\leq \epsilon}P(WM)\;dwdm + \frac{\ln\vert \mathcal{M}\vert}{n}\int\limits_{\epsilon <i(W;M) 
\leq \ln\vert \mathcal{M}\vert}P(WM)\;dwdm\\
& \leq \frac{\epsilon}{n} + \frac{\ln\vert \mathcal{M}\vert}{n}P(i(W;M) > \epsilon),
\end{align*}$$

because $$P(WM) \leq P(W)$$ and

$$\int\limits_{i(W;M)\leq -\epsilon}\frac{i(W;M)}{n}P(WM)\;dwdm < 0$$ 

and 

$$\int\limits_{i(W;M) > \ln\vert M\vert}\frac{i(W;M)}{n}P(WM)\;dwdm = 0,$$

since the authors (M. Bloch and J.N. Laneman) suppose $$M$$ to be distributed uniformly over set $$\mathcal{M}$$.

So 

$$\forall \epsilon >0\;\;\;\; \lim\limits_{n\rightarrow \infty}\frac{I(W_n;M_n)}{n}
\leq \lim\limits_{n\rightarrow \infty}\left (\frac{\epsilon}{n}+\frac{\ln \vert \mathcal{M}\vert}{n}P(i(W_n;M_n)>\epsilon)\right ) \Rightarrow \frac{I(W;M)}{n} \preceq i(W;M)$$ 

<br />
<br />
<br />

Now we are one step away from famous Pinsker's inequality. Let's go this step!

Recall two results shown above.
$$E(\vert i(W;M)\vert) \leq I(W;M) + c\sqrt{I(W;M)}$$, where $$c$$ is some constant and
$$\Delta(P(WM),P(W)P(M)) \leq 2E(\vert i(W;M)\vert)$$.

Putting them together yeilds 
$$\begin{align*}
&\frac{1}{2}\Delta(P(WM),P(W)P(M)) \leq I(W;M) + c\sqrt{I(W;M)} \\
&\frac{1}{2}\Delta(P(WM),P(W)P(M)) - I(W;M) \leq c\sqrt{I(W;M)} \\
&\left(\frac{1}{2}\Delta(P(WM),P(W)P(M)) - I(W;M)\right)^2 \leq c^2I(W;M) \\
&\frac{1}{4}\Delta(P(WM),P(W)P(M))^2 - \Delta(P(WM),P(W)P(M))I(W;M) + I(W;M)^2 \leq c^2I(W;M)\\
&\frac{1}{4}\Delta(P(WM),P(W)P(M))^2 \leq c^2I(W;M) + \Delta(P(WM),P(W)P(M))I(W;M) - I(W;M)^2 \\
&\frac{1}{4}\Delta(P(WM),P(W)P(M))^2 < c^2I(W;M) + \Delta(P(WM),P(W)P(M))I(W;M) 
\end{align*}$$

Since $$\Delta(P(WM),P(W)P(M)) < 2$$, then 

$$\Delta(P(WM),P(W)P(M))^2 < aI(W;M),$$ 

where $$a$$ is some constant. The lowest $$a$$ holdig the inequality true is equal 2.

### What security eventually means

As far as the development of cryptography there were a variety of types of security. The first one is perfect security stated by Shannon $$I(W;M)=0$$. It followed by strong security $$\lim_{n\rightarrow \infty} I(W;M)=0$$ and weak security $$\lim_{n\rightarrow \infty} \frac{I(W;M)}{n}=0$$.


It is because of massive pile of equations I've written above that the statement 

$$\text{weak security} \preceq \text{strong security} \preceq \text{perfect security}$$ 

is clear.

What does the weak security mean from information theory point of view? Let's reformulate $$\lim_{n\rightarrow \infty} \frac{I(W;M)}{n}=0$$ as $$\frac{I(M;W)}{n}\leq \tau_n,\;\;\lim_{n\rightarrow \infty}\tau_n=0$$ and recall Fano's lemma. If $$\hat{M}$$ is estimation of $$M$$, then $$H(M|\hat{M})\leq h(p_{err})+p_{err}\log_2(|\mathcal{M}|-1)$$, where $$p_{err}=p(\hat{M}\neq M),\;\;h(p_{err})=-p_{err}\log_2p_{err}-(1-p_{err})\log_2(1-p_{err})$$. Then under condition that $$M$$ is uniformly distributed
$$\begin{align*}
&  H(M|\hat{M})\geq H(M|W),\;\; \max_{p_{err}}h(p_{err})=1\;\Rightarrow\;1+p_{err}\log_2(|\mathcal{M}|-1)\geq H(M|W)\\
&  p_{err}\geq \frac{H(M|W)-1}{\log_2(|\mathcal{M}|-1)}\geq\frac{H(M|W)-1}{\log_2|\mathcal{M}|}\geq\frac{H(M)-n\tau_n}{\log_2|\mathcal{M}|}=\frac{H(M)}{\log_2|\mathcal{M}|}-\frac{n\tau_n}{\log_22^{nR}}=\frac{H(M)}{\log_2|\mathcal{M}|}-\frac{\tau_n}{R}=1-\frac{\tau_n}{R}.
\end{align*}$$
So the probability of an adversary being wrong converges to 1 with speed $$o(1)$$ as length $$n$$ of initial message $$M$$ increases. Bjelakovic, Boche and Sommerfeld proved that for strong security the speed of convergence is exponential, so $$\lim_{n\rightarrow \infty}I(W;M)=0\;\;\;\Rightarrow \;p_{err}=1-2^{-n}$$.

As for perfect security the condition $$I(W;M)=0$$ leads to 

$$\begin{align*}&P(WM)=P(W)P(M) \\
&P(M|W)P(W) = P(W)P(M) \\
&P(M|W) = P(M)
\end{align*}$$

The condition $$P(M\vert W) = P(M)$$ is difficult to guarantee. In practice the more relaxed condition called _semantic security_ is used 

$$P(M|W) \approx_{p} P(M).$$

The $$\approx_{p}$$ means that two distributions can not be computationally distinguished. "Computationally" in turn means the process should end before the heat death of the universe. Simply saying it should be polinomial in time. 

In computational security world an _attack game_ is used to state the security definition. Attack game is played between a challenger and an adversary. A challenger follows fixed protocol, while an adversary may follow arbitrary one. They send messages to each other according to their protocols. The adversary behaviour is described by _advantage_. Adversary's advantage is essentially a number showing how the adversary close to win the game. The particually meaning of "win the game" depends on a security definition which we are trying to challenge.

For semantic security the advantage is
$$\begin{align*}\text{Adv}(W)=\max_{f,M}\left(\max_{\mathcal{A}}P(\mathcal{A}(W)=f(M))-\max_{\text{Gen}}P(\text{Gen}(n)=f(M))\right)
     & \xrightarrow[n\rightarrow \infty]{} 0\end{align*},$$
where $$\mathcal{A}$$ is some polynomial adversary algorithm, $$f$$ -- some deterministic function, $$\text{Gen}$$ -- random generator with output message of length $$n$$.
So this formulation is close to $$P(M\vert W)\approx P(M)$$. They both mean that even having got the message $$W$$ the adversary can not estimate the initial message $$M$$ better than to guess it randomly without $$W$$.

So the attack game for semantic security is as follows. The adversary sends to a challenger two messages $$M_0$$ and $$M_1$$. the challenger chooses one on them and sends back $$W=M_b$$, where $$b\sim\text{Uniform}(\{0,1\})$$. The adversary should guess whether $$b=0$$ ($$M_b=M_0$$) or $$b=1$$ ($$M_b=M_1$$). So the advantage is

$$\begin{align*}\forall \mathcal{A} \in \text{Poly}\;\; \text{Adv}(W)&=\max_{M_1,M_0}|P(\mathcal{A}(W)=1)-P(\mathcal{A}(W)=0)|\rightarrow 0 \\ 
& \sim \max_{\mathcal{A}\in \text{Poly},M_1,M_0}|P(\mathcal{A}(W)=1)-P(\mathcal{A}(W)=0)|\\
           =&\max_{\mathcal{A}\in \text{Poly}, M_0,M_1}2P(\mathcal{A}(W)=b)-1\rightarrow 0
\end{align*}$$

Wait, but the last one is exactly the advantage of distinguishing security! Yes, there is also distinguishing security notion. Imagine there are two messages $$M_0$$ and $$M_1$$ and two intercepted messages $$W'$$ and $$W''$$. Distinguishing security means that the adversary can not relate the intercepted messages to initinal ones. So intercepted message doesn't just reveal anything about plain message, but it is impossible to say which particular plain message it belongs to.

Let's show that 
$$\text{Distinguishing security }\leftrightarrow\text{ Semantic security: }\text{Adv}_{\text{Semantic}}\leq \text{Adv}_{\text{Distinguishing}}\leq 2\text{Adv}_{\text{Semantic}}$$

Let $$M_0,M_1$$ be independent and distributed as $$M$$
$$\begin{align*}
  &P\left(\mathcal{A}(M_0,M_1,W|b=1)=1\right)=P\left(\mathcal{A}(W)=f(M)\right),\\
      &P(\mathcal{A}(M_0,M_1,W|b=0)=1)\leq P(\text{Gen}(n)=f(M))
\end{align*}$$
$$\begin{align*}
      P(\mathcal{A}(W)=f(M))-P(\text{Gen}(n)=f(M))&\leq P(\mathcal{A}(M_0,M_1,W|b=1)=1)-P(\mathcal{A}(M_0,M_1,W|b=0)=1)\\ &\leq \max_{M_0,M_1} 2P(\mathcal{A}(M_0,M_1,W)=b)-1
  \end{align*}$$
So $$\text{Adv}_{\text{Semantic}}\leq \text{Adv}_{\text{Distinguishing}}$$.

$$M_b\sim \text{Uniform}(\{M_0,M_1\})),\;\; b\sim \text{Uniform}(\{0,1\})\Rightarrow\text{ to define }b\;\sim\text{ to define }M_b$$

$$\begin{align*}
      \frac{1}{2}\text{Adv}_{\text{Distinguishing}}&=\max_{\mathcal{A},M_0,M_1}\left(P(\mathcal{A}(M_0,M_1,W)=b)-\frac{1}{2}\right)\\
      &=\max_{\mathcal{A},M_0,M_1}\left(P(\mathcal{A}(W)=M_b)-\max_{\text{Gen}}P(\text{Gen}(n)=M_b)\right)\leq \text{Adv}_{\text{Semantic}}
\end{align*}$$

Advantage of strong security is straghtforward from definition: $$\text{Adv}(W)_{\text{Strong}}=\max_{M}I(W;M)$$.
Let's show that $$\text{Distinguishing security }\leftrightarrow \text{ Strong security}$$.

According to Pinsker's inenquality 
$$\text{Adv}(W)_{\text{Strong}}=\max_{M}I(W;M) \geq \frac{1}{2}\max_M \Delta(P(WM),P(W)P(M))^2$$
$$\begin{align*}\Delta(P(WM),P(W)P(M)) &= \sum_{m,w}|P(W=w,M=m) - P(W=w)P(M=m)| \\
&=\sum_{w,m}|P(W=w|M=m)P(M=m) - P(W=w)P(M=m)| \\
&=\sum_m P(M=m)\sum_w|P(W=w|M=m) -P(W=w)| \\
&=\sum_m P(M=m)\sum_w|P(W=w|M=m) - \sum_{\tilde{m}}P(W=w|M=\tilde{m})P(M=\tilde{m})|\\
&=\sum_{m}P(M=m)\sum_w |\sum_{\tilde{m}}\left(P(W=w|M=m) -P(W=w|M=\tilde{m})\right)P(M=\tilde{m})|
\end{align*}$$
Seeing as $$M\sim \text{Uniform}(\{M_0,M_1\}))$$, the only one non-zero term of sum over $$\tilde{m}$$ is $$P(W=w|M=M_0) -P(W=w|M=M_1)$$ and $$P(M)=\frac{1}{2}$$.
$$\begin{align*}\Delta(P(WM),P(W)P(M)) &= \sum_m\frac{1}{2}\sum_w\frac{1}{2}|P(W=w|M=M_0)-P(W=w|M=M_1)| \\
&=\frac{1}{2}\sum_w|P(W=w|M=M_0)-P(W=w|M=M_1)|\\
&=\frac{1}{2}\Delta(P(W|M_0),P(W|M_1))\end{align*}$$

$$\text{Adv}(W)_{\text{Strong}}=\max_M I(W;M) \geq \frac{1}{2}\max_M \Delta(P(WM),P(W)P(M))^2 = \frac{1}{4}\max_{M_0,M_1}\Delta(P(W\vert M_0),P(W\vert M_1))^2=\frac{1}{4}\text{Adv}_{\text{Distinguishing}}^2$$

Now let's consider $$I(W;M)$$.

$$\begin{align*}I(M;W)=H(W)-\sum_mP(M=m)H(W\vert M=m)=\sum_mP(M=m)(H(W)-H(W\vert M=m))\end{align*}.$$

Let $$m' = \text{argmax}_m(H(W)-H(W\vert M=m))$$ and $$P(W\vert M=m')=Q(W)$$.

$$\begin{align*}I(M;W) &\leq \sum_m P(M=m)(H(W)-H(W|M=m')) = H(W)-H(W|M=m') \\
&=-\sum_w \left( P(W=w)\log P(W=w) - P(W=w|M=m')\log P(W=w|M=m') \right )\\
&=-\sum_w \left( P(W=w)\log\frac{P(W=w)}{2} - P(W=w|M=m')\log\frac{P(W=w|M=m')}{2} \right )\\
&=-2\sum_w \left( \frac{P(W=w)}{2}\log \frac{P(W=w)}{2} - \frac{P(W=w|M=m')}{2}\log \frac{P(W=w|M=m')}{2} \right)\\
&=-2\sum_w \left( \frac{P(W=w)}{2}\log \frac{P(W=w)}{2} - \frac{Q(W=w)}{2}\log \frac{Q(W=w)}{2} \right)\\
&\leq -2 \left |\sum_w \frac{P(W=w)}{2}\log \frac{P(W=w)}{2} - \frac{Q(W=w)}{2}\log \frac{Q(W=w)}{2} \right |\\
&\leq -2 \sum_w \left |\frac{P(W=w)}{2}\log \frac{P(W=w)}{2} - \frac{Q(W=w)}{2}\log \frac{Q(W=w)}{2} \right |
\end{align*}$$

Let's consider function $$f(x)=-x\log (x),\;\; f(0)=f(1)=0$$. The maximum absolute slope of any chord from $$x$$ to $$x+\delta$$ for $$\delta \leq \frac{1}{2}$$ is achieved at either end $$x=0$$ or $$x=1-\delta$$, so $$\vert f(x) -f(x+\delta)\vert \leq \max\{f(\delta),f(1-\delta)\}$$. Since $$f(1-\delta)<f(\delta)$$ for $$0<\delta\leq\frac{1}{2}$$ so $$\vert f(x) -f(x+\delta)\vert\leq -\delta \log(\delta)$$.

Seeing as $$\left \vert \frac{P(W)}{2} -\frac{Q(W)}{2}\right \vert \leq \frac{1}{2}\;\; \forall W$$
$$\begin{align*}I(M;W) &\leq -2 \sum_w \left |\frac{P(W=w)}{2}\log \frac{P(W=w)}{2} - \frac{Q(W=w)}{2}\log \frac{Q(W=w)}{2} \right | \\
&\leq -2\sum_w \left |\frac{P(W=w)}{2}-\frac{Q(W=w)}{2}\right |\log \left |\frac{P(W=w)}{2}-\frac{Q(W=w)}{2} \right | \\
&=-2 \Delta(P(W),Q(W))\sum_w\frac{\left |\frac{P(W=w)}{2}-\frac{Q(W=w)}{2}\right |}{\Delta(P(W),Q(W))}\log\frac{\left |\frac{P(W=w)}{2}-\frac{Q(W=w)}{2}\right |}{\Delta(P(W),Q(W))}\Delta(P(W),Q(W))\\
&=-2 \Delta(P(W),Q(W)) \sum_w \frac{\left |\frac{P(W=w)}{2}-\frac{Q(W=w)}{2}\right |}{\Delta(P(W),Q(W))}\left (\log\frac{\left |\frac{P(W=w)}{2}-\frac{Q(W=w)}{2}\right |}{\Delta(P(W),Q(W))}+\log(\Delta(P(W),Q(W)))\right )\\
&=-2 \Delta(P(W),Q(W)) \sum_w \frac{\left |\frac{P(W=w)}{2}-\frac{Q(W=w)}{2}\right |}{\Delta(P(W),Q(W))}\log\frac{\left |\frac{P(W=w)}{2}-\frac{Q(W=w)}{2}\right |}{\Delta(P(W),Q(W))}\\
&-2\Delta(P(W),Q(W))\log(\Delta(P(W),Q(W)))\sum_w\frac{\left |\frac{P(W=w)}{2}-\frac{Q(W=w)}{2}\right |}{\Delta(P(W),Q(W))}\\
&=-2 \Delta(P(W),Q(W)) \sum_w \frac{\left |\frac{P(W=w)}{2}-\frac{Q(W=w)}{2}\right |}{\Delta(P(W),Q(W))}\log\frac{\left |\frac{P(W=w)}{2}-\frac{Q(W=w)}{2}\right |}{\Delta(P(W),Q(W))}\\
&-2\Delta(P(W),Q(W))\log(\Delta(P(W),Q(W)))\\
&\leq 2\Delta(P(W),Q(W))\log|\mathcal{W}|-2\Delta(P(W),Q(W))\log(\Delta(P(W),Q(W)))\\
&=2\Delta(P(W),Q(W))\log\frac{|\mathcal{W}|}{\Delta(P(W),Q(W))},
\end{align*}$$
because the first term is entropy and can be upper bounded by the entropy of uniform distribution.

$$\begin{align*}\Delta(P(W),Q(W))&=\sum_m\left|P(W=w)-P(W=w|M=m')\right|\\
&=\sum_w\left|P(W=w|M=m')-\sum_mP(W=w|M=m)P(M=m)\right|\\
&=\sum_w\left|\sum_m \left ( P(W=w|M=m')-P(W=w|M=m)\right ) P(M=m)\right|\\
&=\sum_w\sum_m\left|P(W=w|M=m')-P(W=w|M=m)\right|P(M=m)\\
&\leq \sum_mP(M=m)\max_{m,m'}\sum_w\left|P(W=w|M=m')-P(W=w|M=m)\right|\\
&=\sum_mP(M=m)\text{Adv}_\text{Distinguishing}=\text{Adv}_\text{Distinguishing}
\end{align*}$$

$$\begin{align*}I(M;W) \leq 2\Delta(P(W),Q(W))\log\frac{\vert \mathcal{W} \vert}{\Delta(P(W),Q(W))} &\leq 2\text{Adv}_\text{Distinguishing}\log\frac{\vert \mathcal{W}\vert}{\text{Adv}_\text{Distinguishing}}\\
\max_MI(M;W)=\text{Adv}_\text{Strong} &\leq \max_M 2\text{Adv}_\text{Distinguishing}\log\frac{\vert \mathcal{W}\vert}{\text{Adv}_\text{Distinguishing}}\\
\frac{1}{4}\text{Adv}_{\text{Distinguishing}}^2 \leq \text{Adv}_\text{Strong} &\leq 2\text{Adv}_\text{Distinguishing}\log\frac{\vert \mathcal{W} \vert}{\text{Adv}_\text{Distinguishing}}\end{align*}$$


So finally
$$\text{Weak security} \preceq (\text{Strong security} \leftrightarrow \text{Distinguishing security} \leftrightarrow \text{Semantic security}) \preceq  \text{Perfect security}$$

<br />
<br />
<br />
References  
M. S. Pinsker "Information and Information Stability of Random Variables and Processes"  
Mihir Bellare, Stefano Tessaro, Alexander Vardy [A Cryptographic Treatment of the Wiretap Channel](https://arxiv.org/pdf/1201.2205)  
Dan Boneh and Victor Shoup [A Graduate Course in Applied Cryptography](https://crypto.stanford.edu/~dabo/cryptobook/BonehShoup_0_6.pdf)  
Cong Ling, Laura Luzzi, Jean-Claude Belfiore, Damien Stehle [Semantically Secure Lattice Codes for the Gaussian Wiretap Channel](https://arxiv.org/pdf/1210.6673)  
Sergio Verdu [Total Variation Distance and the Distribution of Relative Information]("https://ita.ucsd.edu/workshop/14/files/paper/paper_374.pdf)  
Matthieu Bloch and J. Nicholas Laneman [On the Secrecy Capacity of Arbitrary Wiretap Channels](https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=506f94a7f34bc1909c76a40bd5528c5fa35007a1)  
Mihir Bellare, Stefano Tessaro, Alexander Vardy [Semantic Security for the Wiretap Channel](https://homes.cs.washington.edu/~tessaro/papers/wiretap-proc.pdf)
T. M. Cover and J. A. Thomas. Elements of Information Theory

I. Bjelakovic , H. Boche, J. Sommerfeld Secrecy results for compound wiretap channels
