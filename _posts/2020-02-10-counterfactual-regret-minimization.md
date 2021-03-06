---
layout: post
title: Counterfactual Regret Minimization
lang: en
categories:
    - StatML
tags:
    - RL
    - Regret
---

In this post, I introduce you the Counterfactual Regret Minimization (CFR Algorithm). It is mainly used for the algorithm to figure out the optimal strategy of a extensive-form game with incomplete information such as Poker and Mahjong.


### Extensive-form Game

- Set, variables
  - $$N: $$ set of players
    - $$i \in N$$: player
  - $$A :$$ set of actions
    - $$a \in A: $$ action
  - $$H: $$set of sequences
    - $$h \in H: $$ sequences (= possible history of actions, $$h = (a_1, \dots, a_t$$)
    - $$Z \subseteq H: $$ set of terminal histories. $$Z = \{z \in H \vert \forall h \in H, z \notin h \}$$
    - $$z \in Z$$: sea
- Function, relations
  - $$u_i: Z \to \mathbb{R}: $$ utility function of player $$i$$
  - $$\sigma_i: A \to [0,1]$$ a strategy of player $$i$$, probability distribution on action set $$A$$.
  - $$\sigma~: A^N \to [0,1]$$ a strategy profile, $$\sigma := (\sigma_1, \dots, \sigma_N)$$
  - $$\pi^{\sigma}_i: H \to [0,1]: $$ probability of history $$h$$ under a strategy $$\sigma_i$$ of player $$i$$ 
  - $$\pi^{\sigma}: H^N \to [0,1]: $$ probability of history $$h$$ under a strategy profile  $$\sigma$$
  


Then, you can also interplate $$u_i$$ as the function mapping a storategy profile $$\sigma$$ to its utility. 


$$
\begin{align}
u_i(\sigma) 
&= \sum_{h \in Z} u_i(h) \pi^{\sigma}(h) \\
&= \sum_{h \in Z} u_i(h) \prod_{i \in N} \pi^{\sigma}_i(h)
\end{align}
$$

<br>

### Nash equilibrium

**Definition:** $$(\text{Nash equilibrium})$$

In $$N$$-player extensive game, a strategy profile $$\acute{\sigma} := (\acute{\sigma_1}, \dots, \acute{\sigma_N})$$ is the Nash equilibrium if and only if the followings holds.

$$
\begin{aligned}
u_1(\acute{\sigma_1}, \dots, \acute{\sigma_N}) 
&\geq \underset{\sigma_1}{\rm max} ~ u_1(\sigma_1, \acute{\sigma_{-1}}) \\
u_2(\acute{\sigma_1}, \dots, \acute{\sigma_N}) 
&\geq \underset{\sigma_2}{\rm max} ~ u_2(\sigma_2, \acute{\sigma_{-2}}) \\
&~ \vdots \\
u_N(\acute{\sigma_1}, \dots, \acute{\sigma_N}) 
&\geq \underset{\sigma_N}{\rm max} ~ u_N(\sigma_N, \acute{\sigma_{-N}})
\end{aligned}
$$


**Definition: $$\text{(}\varepsilon\text{-Nash equilibrium)}$$**

In $$N$$-player extensive game, a strategy profile $$\acute{\sigma} := (\acute{\sigma_1}, \dots, \acute{\sigma_N})$$ is the $$\varepsilon$$-Nash equilibrium if and only if the followings holds when $$\forall \varepsilon \geq 0$$ is given.

$$
\begin{aligned}
u_1(\acute{\sigma_1}, \dots, \acute{\sigma_N}) + \varepsilon
&\geq \underset{\sigma_1}{\rm max} ~ u_1(\sigma_1, \acute{\sigma_{-1}}) \\
u_2(\acute{\sigma_1}, \dots, \acute{\sigma_N}) + \varepsilon
&\geq \underset{\sigma_2}{\rm max} ~ u_2(\sigma_2, \acute{\sigma_{-2}}) \\
&~ \vdots \\
u_N(\acute{\sigma_1}, \dots, \acute{\sigma_N}) + \varepsilon
&\geq \underset{\sigma_N}{\rm max} ~ u_N(\sigma_N, \acute{\sigma_{-N}})
\end{aligned}
$$

<br>

### Regret matching

- Average overall regret of player $$i$$ at time $$T$$：

$$
R_i^T 
:= \underset{\sigma_i^*}{\rm max} ~
\frac{1}{T} \sum_{t=1}^{T} \left( u_i(\sigma_i^*, \sigma_{-i}^{t}) - u_i(\sigma_i^t, \sigma_{-i}^{t}) \right)
$$



- Average strategy for player $$i$$ from time $$1$$ to $$T$$：

$$
\begin{align}
\overline{\sigma}_i^t(I)(a) 
&:= \frac{\sum_{t=1}^{T} \pi_i^{\sigma^t}(I) \cdot \sigma^t(I)(a)}{\sum_{t=1}^{T} \pi_i^{\sigma^t}(I)} \\
&= \frac{\sum_{t=1}^{T} \sum_{h \in I} \pi_i^{\sigma^t}(h) \cdot \sigma^t(h)(a)}{\sum_{t=1}^{T} \sum_{h \in I} \pi_i^{\sigma^t}(h)} \\
\end{align}
$$

If the average overall regret holds $$R_i^T \leq \varepsilon$$, the average strategy $$\overline{\sigma}_i^t(I)(a) $$ is $$2 \varepsilon-$$Nash equilibrium for player $$i$$ in time $$t$$. So that, in order to derive Nash equilibrium, we should minimize the average overall regret $$R_i^T$$ or its upper bound $$\varepsilon$$ according to $$R_i^T \to 0 ~~ (\varepsilon \to 0)$$.

<br>

### CFR Algorithm

- Counterfactual utility：

$$
\begin{align}
u_i(\sigma, I) = \frac{\sum_{h \in H, h' \in Z} \pi_{-i}^{\sigma}(h)\pi^{\sigma}(h,h')u_i(h) }{\pi_{-i}^{\sigma}(I)}
\end{align}
$$

- immediate counteractual regret of action $$a$$ in Information set $$I$$:

$$
\begin{aligned}
R_{i,imm}^{T}(I, a)
:=
\frac{1}{T} \sum_{t=1}^{T} 
\pi_{-i}^{\sigma^t}(I)
\left( 
u_i(\sigma^t_{I \to a}, I) - u_i(\sigma^t, I) 
\right)
\end{aligned}
$$

- Immediate counterfactual regret of Information set $$I$$：

$$
\begin{aligned}
R_{i,imm}^{T}(I)
&:= \underset{a \in A(I)}{\rm max} ~
\frac{1}{T} \sum_{t=1}^{T} 
\pi_{-i}^{\sigma^t}(I)
\left( 
u_i(\sigma^t_{I \to a}, I) - u_i(\sigma^t, I) 
\right) 
\end{aligned}
$$

The following inequality holds for **the average overall regret** $$R_i^T $$ and **the immediate counterfactual regret**  $$R_{i,imm}^{T}(I)$$:

$$
\begin{aligned}
R_i^T 
\leq \sum_{I \in \mathcal{I}_i} &R_{i,imm}^{T,+}(I) \\
where ~~~
&R_{i,imm}^{T, +}(I) 
:= max(R_{i,imm}^{T}(I), 0)
\end{aligned}
$$

So that, we obtain the sufficient condition of $$R_{i,imm}^{T}(I)$$  for the average strategy $$\overline{\sigma}_i^t(I)(a) $$ to become a Nash equilibrium strategy as below. 

$$
\sum_{I \in \mathcal{I}_i} R_{i,imm}^{T,+}(I) \to 0 ~~~ \Rightarrow ~~~ R_i^T \to 0 ~~~ \Rightarrow ~~~ \varepsilon \to 0.
$$

Now all we need is to minimize the immediate counterfactual regret  $$R_{i,imm}^{T}(I)$$. 

In addition, as can be seen from the above formula, the computational complexity of the CFR algorithm depends on the number of information sets $$I$$. Also, to avoid the complete search of game tree (searching all information sets $$I$$), subsequent algorithms such as CFR + propose an abstraction of the game state.