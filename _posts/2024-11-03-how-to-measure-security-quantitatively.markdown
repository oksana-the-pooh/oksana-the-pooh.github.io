---
layout: post
title:  "How to Measure Security"
date:   2024-11-03 20:08:00 +0300
categories: cryptography, information_theory, probability_theory
use_math: true
---
Can we measure security quantitatively?

We use a lot of cryptographic tools not even noticing it. Almost every website exploring uses HTTPS to establish secure connection. In order to establish it a web browser and a website server must agree on what cipher to use for data encyption, what Message Authentication Code algorithm to use for a message integrity checking. Also a browser authenticates web site certificate using digital signature. When you pay by a credict card using PayPass/PayWare the card signs a transaction by it's digital signature. All this tools serve to guarantee a security. But what does security mean? May one cipher be more secure than another one and can we see that quantitatively?

### How to approach the question

For most of its history ctyptography was like a game where each side trying to outwit the other one. An encryption scheme or cipher was considered to be secure unless someone can successfully attack it. Then cryptographers developed some ad hoc defence and waited for a next attakers' move. Nowdays cryptography still doesn't look like the true science with clear and precise system of concepts, proofs and so on. Thanks to Claude Shannon there is a benchmark of a security. 

Let's consider every message $$m$$ to be sent securelly as a sample of some distribution $$P(M)$$. By intercepting a message $$m$$ an attaker got some message $$w$$, which in turn is a sample of some distribution $$P(W)$$. According to Shannon a link between $$P(MW)$$ and $$P(M)P(W)$$ is the point.

### How to measure

So we can consider all possible distances between distributions. Namely,

- mutual information $$I(M; W) = D_{KL}(P(MW)\|P(W)P(M))$$
- variation distance $$\Delta(P(MW), P(M)P(W))$$, where $$\Delta(P(X),P(X')) = \sum_{x \in X} \vert P(X=x) - P(X'=x)\vert$$
- information density $$i(M; W) = \ln\frac{P(WM)}{P(W)P(M)}$$
- mutual information per symbol $$\frac{I(W; M)}{n}$$
- variation density per symbol $$\frac{\Delta(P(WM),P(W)P(M))}{n}$$
- information density per symbol $$\frac{i(W; M)}{n}$$


where $$n$$ is a length of a message. Why is a length important? Imagine someone eavesdrops you through the window from the outside. If you said only one word an eavesdropper could easily catch it if it wasn't too noisy outside at that time. But if you told long detailed story an eavesdropper might not catch a meaning of the story, because he missed some important words due to street noise.

### What measure to choose

To choose the best distance or just order them we need to determine comparison operator.

They say $$\delta_{n}^{(1)}$$ is stronger than $$\delta_{n}^{(2)}$$  ($$\delta_n^{(2)}\preceq \delta_b^{(1)}$$) if convergence in probability to zero of $$\delta_{n}^{(1)}$$ results in convergence in probability to zero of $$\delta_{n}^{(2)}$$, i.e.
$$
\forall \epsilon >0\; \lim\limits_{n\rightarrow \infty}P(|\delta_n^{(1)}|>\epsilon) \Rightarrow \lim\limits_{n\rightarrow \infty}P(|\delta_n^{(2)}|>\epsilon)=0.
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
\frac{I(W;M)}{n} = E(\frac{i(W;M)}{n}) &=\int\limits_{i(W;M)\leq -\epsilon}\frac{i(W;M)}{n}P(WM)\;dwdm +\int\limits_{-\epsilon < i(W;M)\leq \epsilon}\frac{i(W;M)}{n}P(WM)\;dwdm\\
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
\leq \lim\limits_{n\rightarrow \infty}(\frac{\epsilon}{n}+\frac{\ln \vert\mathcal{M}\vert}{n}P(i(W_n;M_n)>\epsilon)) \Rightarrow \frac{I(W;m)}{n} \preceq i(W;M)$$



### What does security mean eventually

- Совершенная секретность $I(W;M)=0$
- Сильная секретность $\lim_{n\rightarrow \infty} I(W;M)=0$
- Слабая секретность $\lim_{n\rightarrow \infty} \frac{I(W;M)}{n}=0$

- $$I(W;M)=0 \;\;\;\Rightarrow \; |K|\geq |M|$$
- $$\lim_{n\rightarrow \infty}I(W;M)=0\;\;\;\Rightarrow \;p_{err}=1-2^{-n}$$
- $$\lim_{n\rightarrow \infty}\frac{I(W;M)}{n}=0\;\;\;\Rightarrow \; p_{err}=1-o(1)$$

#  \begin{lem}
#    $p_{err}=p(\hat{M}\neq M),\;\;h(p_{err})=-p_{err}\log_2p_{err}-(1-p_{err})\log_2(1-p_{err})$\\
#    \vspace*{0.3cm}
#    $H(M|\hat{M})\leq h(p_{err})+p_{err}\log_2(|M|-1)$
#  \end{lem}
#  \vspace{0.5cm}
#  $\frac{I(M;W)}{n}\leq \tau_n\;\;\lim_{n\rightarrow \infty}\tau_n=0$\\
#  $H(M|\hat{M})\geq H(M|W),\;\; max_{p_{err}}h(p_{err})=1\;\Rightarrow\;1+p_{err}\log_2(|M|-1)\geq H(M|W)$\\
#  $p_{err}\geq \frac{H(M|W)-1}{\log_2(|M|-1)}\geq\frac{H(M|W)-1}{\log_2|M|}\geq\frac{H(M)-n\tau_n}{\log_2|M|}=\frac{H(M)}{\log_2|M|}-\frac{n\tau_n}{\log_22^{nR}}=\frac{H(M)}{\log_2|M|}-\frac{\tau_n}{R}$
#
#
#Совершенная секретность: $P(WM)\approx P(W)P(M)$\\
#  Семантическая секретность: $P(WM)\approx_{p} P(W)P(M)$\\
#
#\begin{equation*}\text{Adv}(W)=\max_{f,M}\left(\max_{\mathcal{A}}P(\mathcal{A}(W)=f(M))-\max_{\text{Gen}}P(\text{Gen}(n)=f(M))\right)
#      %=&\sup_{f,M}\left(2^{-H_{\infty}(f(M)|W)}-2^{-H_{\infty}(f(M))}\right)
#    \xrightarrow[n\rightarrow \infty]{} 0\end{equation*}
#
#
#\framesubtitle{Семантическая секретность}
#       \begin{figure}
#  \begin{tikzpicture}[scale=0.7]
#    \node{};
#    \node(2) at (2.5,0)[draw, rectangle]{\scriptsize{Шифратор}};
#    \node(3) at (10,0.3){};
#    \node(4) at (10,-0.3){};
#    \node(5) at (11,0){\scriptsize{Злоумышленник, $\mathcal{A}$}};
#%    \draw[-](2.east)--(6);
#    \draw[->](3)--(4,0.3)node[above,midway]{\scriptsize{$m_0,\;m_1\;\;\;|m_0|=|m_1|$}};
#    \draw[->](4,-0.3)--(4)node[below,midway]{\scriptsize{$c=F(m_b)=W$}};
#  \end{tikzpicture}
#       \end{figure}
#       \begingroup
#       \footnotesize
#       $b \in \{0,1\}\;\;\;B_b=\{\text{событие: реализована попытка }b\}$\\
#       \begin{equation*}\begin{split}\hspace{-1cm}\forall \mathcal{A} \in \textbf{P}\;\; \text{Adv}(\mathcal{A})=\max_{M_1,M_0}|P(B_1)-P(B_0)|\rightarrow 0 \sim &\max_{\mathcal{A}\in \textbf{P},M_1,M_0}|P(B_1)-P(B_0)|\\
#           =&\max_{\mathcal{A}\in \textbf{P}, M_0,M_1}2P(\mathcal{A}(M_0,M_1,W)=b)-1\rightarrow 0
#       \end{split}\end{equation*}
#         \endgroup
#
#  Но это distinguish security!
#
# \framesubtitle{Distinguish security $\leftrightarrow$ Семантическая секретность}
#  \begin{them}
#    $\text{Adv}_{\text{Семантическая}}\leq \text{Adv}_{\text{Distinguish}}\leq 2\text{Adv}_{\text{Семантическая}}$
#  \end{them}
#  \vspace{0.2cm}
#  \begingroup
#  \tiny
#  $\square$ $M_0,M_1$ независимы и распределены как $M$
#  \begin{equation*}
#    \begin{split}
#  &P\left(\mathcal{A}(M_0,M_1,W|b=1)=1\right)=P\left(\mathcal{A}(W)=f(M)\right),\\
#      &P(\mathcal{A}(M_0,M_1,W|b=0)=1)\leq P(\text{Gen}(n)=f(M))
#    \end{split}
#  \end{equation*}
#  \begin{equation*}
#    \begin{split}
#      P(\mathcal{A}(W)=f(M))-P(\text{Gen}(n)=f(M))&\leq P(\mathcal{A}(M_0,M_1,W|b=1)=1)-P(\mathcal{A}(M_0,M_1,W|b=0)=1)\\ &\leq \max_{M_0,M_1} 2P(\mathcal{A}(M_0,M_1,W)=b)-1
#      \end{split}
#  \end{equation*}
#  $M_b\sim \text{Uniform}(\{M_0,M_1\})),\;\; b\sim \text{Uniform}(\{0,1\})\Rightarrow$ определить $b\;\sim$ определить $M_b$
#  \begin{equation*}
#    \begin{split}
#      \frac{1}{2}\text{Adv}_{\text{Distinguish}}=&\max_{\mathcal{A},M_0,M_1}\left(P(\mathcal{A}(M_0,M_1,W)=b)-\frac{1}{2}\right)\\
#      =&\max_{\mathcal{A},M_0,M_1}\left(P(\mathcal{A}(W)=M_b)-\max_{\text{Gen}}P(\text{Gen}(n)=M_b)\right)\leq \text{Adv}_{\text{Семантическая}}
#      \end{split}
#  \end{equation*}
#
#\framesubtitle{Distinguish security $\leftrightarrow$ Сильная секретность}
#  \begingroup
#  \tiny
#  %\frametitle{}
#  Сильная секретность: $\text{Adv}(W)_{\text{Сильная}}=I(W;M)$\\
#  \vspace{0.3cm}
#  $\text{Adv}(W)_{\text{Distinguish}}\leq \sqrt{2\text{Adv}(W)_{\text{Сильная}}}$ (нер-во Пинскера)
#  \begin{them}
#    \begin{equation*}
#      \text{Adv}(W)_{\text{Сильная}}\leq 2 \text{Adv}(W)_{\text{Distinguish}}\log_2\frac{2^n}{\text{Adv}(W)_{\text{Distinguish}}}
#      \end{equation*}
#  \end{them}
#  $\square$
#  $f(x) = \min(2x\log\frac{2^n}{x},1)$. $I(M;W)=\sum_{m}P(M=m)(H(W)−H(W|M=m))$. $m=argmax(H(W)-H(W|M=m))$. Пусть $x=\Delta(P(W),P(W|M=m))$.\\
#  \begin{equation*}\hspace{-1.7cm}\begin{split}I(M;W)&\leq H(W)−H(W|M=m)\\ &\leq f(x),\text{ т.к. }H(X)-H(Y)\leq 2\Delta(P(X),P(Y))\log_2\frac{N}{\Delta(P(X),P(Y))},N=|\text{supp}(P(X))\cup \text{supp}(P(Y))|\\
#      & \leq f(\max_{m_0,m_1}\Delta(P(W|M=m_0),P(W|M=m_1)) \text{ т.к. }x\leq \max_{m_0,m_1}\Delta(P(W|M=m_0),P(W|M=m_1))\;\;\forall M 
#      \end{split}
#    \end{equation*}
#  \begin{flushright}
#    $\blacksquare$
#    \end{flushright}
#  \endgroup
#\end{frame}
#\begin{frame}
#  \frametitle{Что такое ``секретно'' и что все это значит?}
#  \framesubtitle{Связь между разными видами секретности}
#  \begin{equation*}\text{Слабая} \preceq (\text{Сильная} \leftrightarrow \text{Distinguish} \leftrightarrow \text{Семантическая}) \preceq  \text{Совершенная}\end{equation*}
#
